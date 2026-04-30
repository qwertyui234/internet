Хайдаров Тимур Усманович
Описание приложения Random Task Generator
Random Task Generator — GUI‑приложение на Python для генерации случайных задач с историей, фильтрацией и сохранением данных. Подходит для самоорганизации, тайм‑менеджмента и формирования полезных привычек.

Основные возможности
Генерация случайных задач из общего списка (предопределённых и добавленных пользователем).

Добавление новых задач с выбором категории («Учёба», «Спорт», «Работа»).

Ведение истории всех сгенерированных задач с нумерацией.

Фильтрация истории по типу задач.

Сохранение и загрузка данных в JSON‑файл (task_history.json).

Валидация ввода — проверка на пустые задачи.

Используемые библиотеки
tkinter — создание графического интерфейса;

ttk — улучшенные виджеты (выпадающий список);

messagebox — отображение уведомлений;

scrolledtext — прокручиваемое текстовое поле для истории;

json — работа с JSON‑файлами;

os — проверка существования файлов;

random — выбор случайных элементов.

Интерфейс приложения
«Новая задача» — поле для ввода текста задачи.

«Тип задачи» — выпадающий список с вариантами: «Учёба», «Спорт», «Работа» (по умолчанию — «Учёба»).

«Добавить задачу» — кнопка для добавления новой задачи в общий список.

«Сгенерировать задачу» (зелёная) — генерирует случайную задачу из списка и отображает её.

«Текущая задача» — область отображения последней сгенерированной задачи (выделена синим цветом).

«Фильтр по типу» — выпадающий список для фильтрации истории: «Все», «Учёба», «Спорт», «Работа».

«Применить фильтр» — применяет выбранный фильтр к истории задач.

«Сбросить фильтр» — сбрасывает фильтр и показывает всю историю.

«История задач» — прокручиваемый список всех сгенерированных задач с нумерацией.

«Сохранить историю» — сохраняет задачи и историю в файл task_history.json.

«Загрузить историю» — загружает данные из файла task_history.json.

Примеры использования
Пример 1. Базовая работа
Запустите приложение.

Нажмите «Сгенерировать задачу».

В поле «Текущая задача» появится случайная задача, например: Пробежка 3 км [Спорт].

Эта задача добавится в «Историю задач» под номером 1.

Пример 2. Добавление новой задачи
В поле «Новая задача» введите: Почитать книгу.

Выберите тип «Учёба».

Нажмите «Добавить задачу». Появится сообщение «Задача добавлена!».

Нажмите «Сгенерировать задачу» — новая задача может выпасть в следующий раз.

Пример 3. Фильтрация истории
Сгенерируйте несколько задач разных типов.

В фильтре «Фильтр по типу» выберите «Спорт».

Нажмите «Применить фильтр». В истории останутся только задачи со пометкой [Спорт].

Нажмите «Сбросить фильтр», чтобы увидеть всю историю.

Пример 4. Сохранение и загрузка данных
Добавьте несколько задач и сгенерируйте 5–10 случайных задач.

Нажмите «Сохранить историю». Появится сообщение «История сохранена в task_history.json».

Закройте приложение.

Откройте его снова.

Нажмите «Загрузить историю». История и список задач восстановятся из файла.

Пример 5. Обработка ошибок
Если нажать «Добавить задачу» с пустым полем «Новая задача», появится сообщение: «Задача не может быть пустой!».

Если попытаться загрузить историю без файла task_history.json, появится предупреждение: «Файл истории не найден!».
import tkinter as tk
from tkinter import ttk, messagebox, scrolledtext
import json
import os
import random

import tkinter as tk
from tkinter import ttk, messagebox, scrolledtext
import json
import os
import requests

class GitHubUserTracker:
    def __init__(self, root):
        self.root = root
        self.root.title("GitHub User Tracker")

        # Данные
        self.found_users = []
        self.favorites = []
        self.load_favorites()

        self.setup_ui()

    def setup_ui(self):
        # Поле для ввода имени пользователя GitHub
        tk.Label(self.root, text="Имя пользователя GitHub:").grid(row=0, column=0, sticky="w", padx=5, pady=2)
        self.username_entry = tk.Entry(self.root, width=30)
        self.username_entry.grid(row=0, column=1, padx=5, pady=2)

        # Кнопка поиска
        tk.Button(self.root, text="Найти пользователя", command=self.search_github_user, bg="lightblue").grid(
            row=1, column=0, columnspan=2, pady=5
        )

        # Отображение результатов поиска
        tk.Label(self.root, text="Найденные пользователи:").grid(row=2, column=0, sticky="w", padx=5, pady=2)
        self.results_text = scrolledtext.ScrolledText(self.root, width=50, height=8)
        self.results_text.grid(row=3, column=0, columnspan=2, padx=5, pady=5)

        # Кнопки для работы с избранным
        tk.Button(self.root, text="Добавить в избранное", command=self.add_to_favorites).grid(row=4, column=0, pady=5)
        tk.Button(self.root, text="Удалить из избранного", command=self.remove_from_favorites).grid(row=4, column=1, pady=5)

        # Отображение избранных пользователей
        tk.Label(self.root, text="Избранные пользователи:").grid(row=5, column=0, sticky="w", padx=5, pady=2)
        self.favorites_text = scrolledtext.ScrolledText(self.root, width=50, height=8)
        self.favorites_text.grid(row=6, column=0, columnspan=2, padx=5, pady=5)

        # Кнопки сохранения/загрузки избранного
        tk.Button(self.root, text="Сохранить избранное", command=self.save_favorites).grid(row=7, column=0, pady=5)
        tk.Button(self.root, text="Загрузить избранное", command=self.load_favorites).grid(row=7, column=1, pady=5)

        # Обновляем отображение избранного
        self.update_favorites_display()

    def search_github_user(self):
        username = self.username_entry.get().strip()

        # Валидация ввода
        if not username:
            messagebox.showerror("Ошибка", "Введите имя пользователя GitHub!")
            return

        if ' ' in username:
            messagebox.showerror("Ошибка", "Имя пользователя не должно содержать пробелов!")
            return

        try:
            # Запрос к GitHub API
            response = requests.get(f"https://api.github.com/users/{username}")

            # Обработка статусов ответа
            if response.status_code == 404:
                messagebox.showwarning("Предупреждение", "Пользователь не найден!")
                return
            elif response.status_code == 403:
                messagebox.showerror(
                    "Ошибка",
            "Превышен лимит запросов к API. Попробуйте позже."
        )
                return
            elif response.status_code != 200:
                messagebox.showerror("Ошибка", f"Ошибка API: {response.status_code}")
                return

            # Успешный ответ
            user_data = response.json()
            self.found_users = [user_data]
            self.update_results_display()

        except requests.exceptions.ConnectionError:
            messagebox.showerror("Ошибка сети", "Проверьте подключение к интернету!")
        except Exception as e:
            messagebox.showerror("Ошибка", f"Произошла ошибка: {e}")

    def update_results_display(self):
        self.results_text.delete(1.0, tk.END)
        for user in self.found_users:
            display_text = (
                f"Имя: {user.get('name', 'Не указано')}\n"
                f"Логин: {user['login']}\n"
                f"Публичных репозиториев: {user['public_repos']}\n"
                f"Подписчиков: {user['followers']}\n"
                f"URL: {user['html_url']}\n"
                f"{'-'*40}\n"
            )
            self.results_text.insert(tk.END, display_text)

    def add_to_favorites(self):
        if not self.found_users:
            messagebox.showwarning("Предупреждение", "Сначала найдите пользователя!")
            return

        user = self.found_users[0]  # Берём первого найденного
        if user not in self.favorites:
            self.favorites.append(user)
            messagebox.showinfo("Успех", f"Пользователь {user['login']} добавлен в избранное!")
            self.update_favorites_display()
        else:
            messagebox.showinfo("Информация", "Этот пользователь уже в избранном!")

    def remove_from_favorites(self):
        selected_text = self.favorites_text.get("sel.first", "sel.last")
        if not selected_text:
            messagebox.showwarning("Предупреждение", "Выберите пользователя для удаления!")
            return

        # Извлекаем логин из выделенного текста
        lines = selected_text.split('\n')
        for line in lines:
            if line.startswith("Логин: "):
                login = line.replace("Логин: ", "")
                break
        else:
            messagebox.showerror("Ошибка", "Не удалось определить пользователя!")
            return

        # Удаляем пользователя из избранного
        self.favorites = [user for user in self.favorites if user['login'] != login]
        messagebox.showinfo("Успех", f"Пользователь {login} удалён из избранного!")
        self.update_favorites_display()

    def update_favorites_display(self):
        self.favorites_text.delete(1.0, tk.END)
        for user in self.favorites:
            display_text = (
                f"Имя: {user.get('name', 'Не указано')}\n"
                f"Логин: {user['login']}\n"
                f"Публичных репозиториев: {user['public_repos']}\n"
                f"Подписчиков: {user['followers']}\n"
                f"URL: {user['html_url']}\n"
                f"{'-'*40}\n"
            )
            self.favorites_text.insert(tk.END, display_text)

    def save_favorites(self):
        try:
            with open("github_favorites.json", "w", encoding="utf-8") as f:
                json.dump(self.favorites, f, ensure_ascii=False, indent=4)
            messagebox.showinfo("Успех", "Избранное сохранено в github_favorites.json")
        except Exception as e:
            messagebox.showerror("Ошибка", f"Не удалось сохранить: {e}")

    def load_favorites(self):
        if os.path.exists("github_favorites.json"):
            try:
                with open("github_favorites.json", "r", encoding="utf-8") as f:
                    self.favorites = json.load(f)
                self.update_favorites_display()
                messagebox.showinfo("Успех", "Данные загружены из github_favorites.json")
            except Exception as e:
                messagebox.showerror("Ошибка", f"Не удалось загрузить: {e}")
        else:
            messagebox.showwarning("Предупреждение", "Файл избранного не найден!")

    def load_favorites(self):
        """Загружает избранных пользователей из файла"""
        if os.path.exists("github_favorites.json"):
            try:
