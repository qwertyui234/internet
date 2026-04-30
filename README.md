import tkinter as tk
from tkinter import ttk, messagebox, scrolledtext
import json
import os
import random

class RandomTaskGenerator:
    def __init__(self, root):
        self.root = root
        self.root.title("Random Task Generator")
        
        # Предопределённые задачи с типами
        self.default_tasks = [
            {"task": "Прочитать статью", "type": "Учёба"},
            {"task": "Сделать зарядку", "type": "Спорт"},
            {"task": "Написать отчёт", "type": "Работа"},
            {"task": "Изучить новую тему", "type": "Учёба"},
            {"task": "Пробежка 3 км", "type": "Спорт"},
            {"task": "Провести встречу", "type": "Работа"}
        ]
        
        self.tasks = self.default_tasks.copy()
        self.history = []
        self.load_data()
        
        self.setup_ui()
    
    def setup_ui(self):
        # Поле для добавления новой задачи
        tk.Label(self.root, text="Новая задача:").grid(row=0, column=0, sticky="w", padx=5, pady=2)
        self.new_task_entry = tk.Entry(self.root, width=30)
        self.new_task_entry.grid(row=0, column=1, padx=5, pady=2)
        
        # Выбор типа задачи
        tk.Label(self.root, text="Тип задачи:").grid(row=1, column=0, sticky="w", padx=5, pady=2)
        self.task_type = ttk.Combobox(self.root, values=["Учёба", "Спорт", "Работа"], width=27)
        self.task_type.set("Учёба")
        self.task_type.grid(row=1, column=1, padx=5, pady=2)
        
        # Кнопка добавления задачи
        tk.Button(self.root, text="Добавить задачу", command=self.add_task).grid(row=2, column=0, columnspan=2, pady=5)
        
        # Кнопка генерации случайной задачи
        tk.Button(self.root, text="Сгенерировать задачу", command=self.generate_random_task, bg="lightgreen").grid(row=3, column=0, columnspan=2, pady=10)
        
        # Отображение текущей задачи
        tk.Label(self.root, text="Текущая задача:").grid(row=4, column=0, sticky="w", padx=5, pady=2)
        self.current_task_label = tk.Label(self.root, text="Нажмите 'Сгенерировать'", font=("Arial", 10, "bold"), fg="blue")
        self.current_task_label.grid(row=4, column=1, padx=5, pady=2, sticky="w")
        
        # Фильтр по типу
        tk.Label(self.root, text="Фильтр по типу:").grid(row=5, column=0, sticky="w", padx=5, pady=2)
        self.filter_type = ttk.Combobox(self.root, values=["Все", "Учёба", "Спорт", "Работа"], width=27)
        self.filter_type.set("Все")
        self.filter_type.grid(row=5, column=1, padx=5, pady=2)
        tk.Button(self.root, text="Применить фильтр", command=self.apply_filter).grid(row=6, column=0, pady=5)
        tk.Button(self.root, text="Сбросить фильтр", command=self.reset_filter).grid(row=6, column=1, pady=5)
        
        # История задач (прокручиваемый текст)
        tk.Label(self.root, text="История задач:").grid(row=7, column=0, sticky="w", padx=5, pady=2)
        self.history_text = scrolledtext.ScrolledText(self.root, width=40, height=12)
        self.history_text.grid(row=8, column=0, columnspan=2, padx=5, pady=5)
        
        # Кнопки сохранения/загрузки
        tk.Button(self.root, text="Сохранить историю", command=self.save_history).grid(row=9, column=0, pady=5)
        tk.Button(self.root, text="Загрузить историю", command=self.load_history).grid(row=9, column=1, pady=5)
        
        # Обновляем отображение истории
        self.update_history_display()
    
    def add_task(self):
        task_text = self.new_task_entry.get().strip()
        task_type = self.task_type.get()
        
        if not task_text:
            messagebox.showerror("Ошибка", "Задача не может быть пустой!")
            return
        
        new_task = {"task": task_text, "type": task_type}
        self.tasks.append(new_task)
        self.new_task_entry.delete(0, tk.END)
        messagebox.showinfo("Успех", "Задача добавлена!")
    
    def generate_random_task(self):
        if not self.tasks:
            messagebox.showwarning("Предупреждение", "Нет задач для генерации!")
            return
        
        current_task = random.choice(self.tasks)
        task_text = current_task["task"]
        task_type = current_task["type"]
        
        full_task = f"{task_text} [{task_type}]"
        self.current_task_label.config(text=full_task)
        
        # Добавляем в историю
        self.history.append(full_task)
        self.update_history_display()
    
    def update_history_display(self, filtered_history=None):
        self.history_text.delete(1.0, tk.END)
        display_list = filtered_history if filtered_history else self.history
        
        for i, task in enumerate(display_list, 1):
            self.history_text.insert(tk.END, f"{i}. {task}\n")
    
    def apply_filter(self):
        filter_type = self.filter_type.get()
        if filter_type == "Все":
            self.update_history_display()
            return
        filtered = [task for task in self.history if f"[{filter_type}]" in task]
        self.update_history_display(filtered)
    
    def reset_filter(self):
        self.filter_type.set("Все")
        self.update_history_display()
    
    def save_history(self):
        try:
            data = {
                "tasks": self.tasks,
                "history": self.history
            }
            with open("task_history.json", "w", encoding="utf-8") as f:
                json.dump(data, f, ensure_ascii=False, indent=4)
            messagebox.showinfo("Успех", "История сохранена в task_history.json")
        except Exception as e:
            messagebox.showerror("Ошибка", f"Не удалось сохранить: {e}")
    
    def load_history(self):
        if os.path.exists("task_history.json"):
            try:
                with open("task_history.json", "r", encoding="utf-8") as f:
                    data = json.load(f)
                    self.tasks = data.get("tasks", self.default_tasks)
                    self.history = data.get("history", [])
                self.update_history_display()
                messagebox.showinfo("Успех", "Данные загружены из task_history.json")
            except Exception as e:
                messagebox.showerror("Ошибка", f"Не удалось загрузить: {e}")
        else:
            messagebox.showwarning("Предупреждение", "Файл истории не найден!")

# Запуск приложения
if __name__ == "__main__":
    root = tk.Tk()
    app = RandomTaskGenerator(root)
    root.mainloop()
