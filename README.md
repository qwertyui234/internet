import tkinter as tk
from tkinter import ttk, messagebox
import json
import os

class BookTrackerApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Book Tracker")
        self.books = []
        self.load_data()

        # Поля ввода
        tk.Label(root, text="Название книги:").grid(row=0, column=0, sticky="w", padx=5, pady=2)
        self.title_entry = tk.Entry(root, width=30)
        self.title_entry.grid(row=0, column=1, padx=5, pady=2)

        tk.Label(root, text="Автор:").grid(row=1, column=0, sticky="w", padx=5, pady=2)
        self.author_entry = tk.Entry(root, width=30)
        self.author_entry.grid(row=1, column=1, padx=5, pady=2)

        tk.Label(root, text="Жанр:").grid(row=2, column=0, sticky="w", padx=5, pady=2)
        self.genre_entry = tk.Entry(root, width=30)
        self.genre_entry.grid(row=2, column=1, padx=5, pady=2)

        tk.Label(root, text="Количество страниц:").grid(row=3, column=0, sticky="w", padx=5, pady=2)
        self.pages_entry = tk.Entry(root, width=30)
        self.pages_entry.grid(row=3, column=1, padx=5, pady=2)

        # Кнопка добавления
        tk.Button(root, text="Добавить книгу", command=self.add_book).grid(row=4, column=0, columnspan=2, pady=10)

        # Таблица для отображения книг
        self.tree = ttk.Treeview(root, columns=("Title", "Author", "Genre", "Pages"), show="headings")
        self.tree.heading("Title", text="Название")
        self.tree.heading("Author", text="Автор")
        self.tree.heading("Genre", text="Жанр")
        self.tree.heading("Pages", text="Страниц")
        self.tree.grid(row=5, column=0, columnspan=2, padx=5, pady=5)

        # Фильтры
        tk.Label(root, text="Фильтр по жанру:").grid(row=6, column=0, sticky="w", padx=5, pady=2)
        self.filter_genre = tk.Entry(root, width=30)
        self.filter_genre.grid(row=6, column=1, padx=5, pady=2)

        tk.Label(root, text="Фильтр страниц (>):").grid(row=7, column=0, sticky="w", padx=5, pady=2)
        self.filter_pages = tk.Entry(root, width=30)
        self.filter_pages.grid(row=7, column=1, padx=5, pady=2)


        tk.Button(root, text="Применить фильтры", command=self.apply_filters).grid(row=8, column=0, columnspan=2, pady=5)
        tk.Button(root, text="Сбросить фильтры", command=self.reset_filters).grid(row=9, column=0, columnspan=2, pady=5)

        # Кнопки сохранения/загрузки
        tk.Button(root, text="Сохранить данные", command=self.save_data).grid(row=10, column=0, pady=5)
        tk.Button(root, text="Загрузить данные", command=self.load_data).grid(row=10, column=1, pady=5)

    def add_book(self):
        title = self.title_entry.get().strip()
        author = self.author_entry.get().strip()
        genre = self.genre_entry.get().strip()

        # Проверка на пустые поля
        if not title or not author or not genre:
            messagebox.showerror("Ошибка", "Все поля, кроме количества страниц, должны быть заполнены")
            return

        # Проверка количества страниц
        pages_text = self.pages_entry.get().strip()
        if pages_text:
            try:
                pages = int(pages_text)
                if pages <= 0:
                    messagebox.showerror("Ошибка", "Количество страниц должно быть положительным числом")
                    return
            except ValueError:
                messagebox.showerror("Ошибка", "Количество страниц должно быть числом")
                return
        else:
            pages = 0  # Если поле пустое, устанавливаем 0

        # Добавляем книгу в список
        book = {
            "title": title,
            "author": author,
            "genre": genre,
            "pages": pages
        }
        self.books.append(book)

        # Обновляем таблицу
        self.update_table()

        # Очищаем поля ввода
        self.clear_entries()

    def clear_entries(self):
        self.title_entry.delete(0, tk.END)
        self.author_entry.delete(0, tk.END)
        self.genre_entry.delete(0, tk.END)
        self.pages_entry.delete(0, tk.END)

    def apply_filters(self):
        filtered_books = self.books

        # Фильтр по жанру
        genre_filter = self.filter_genre.get().strip().lower()
        if genre_filter:
            filtered_books = [book for book in filtered_books if genre_filter in book["genre"].lower()]

        # Фильтр по страницам
        pages_filter_text = self.filter_pages.get().strip()
        if pages_filter_text:
            try:
                min_pages = int(pages_filter_text)
                filtered_books = [book for book in filtered_books if book["pages"] >= min_pages
            except ValueError:
                messagebox.showerror("Ошибка", "Введите корректное число для фильтра страниц")
                return

        # Обновляем таблицу с отфильтрованными данными
        self.update_table(filtered_books)

    def reset_filters(self):
        self.filter_genre.delete(0, tk.END)
        self.filter_pages.delete(0, tk.END)
        self.update_table()

    def save_data(self):
        try:
            with open("books.json", "w", encoding="utf-8") as f:
                json.dump(self.books, f, ensure_ascii=False, indent=4)
            messagebox.showinfo("Успех", "Данные сохранены в books.json")
        except Exception as e:
            messagebox.showerror("Ошибка", f"Не удалось сохранить данные: {e}")


    def load_data(self):
        if os.path.exists("books.json"):
            try:
                with open("books.json", "r", encoding="utf-8") as f:
                    self.books = json.load(f)
                self.update_table()
                messagebox.showinfo("Успех", "Данные загружены из books.json")
            except Exception as e:
                messagebox.showerror("Ошибка", f"Не удалось загрузить данные: {e}")
        else:
            self.books = []
            self.update_table()

    def update_table(self, books=None):
        # Очищаем таблицу
        for item in self.tree.get_children():
            self.tree.delete(item)

        # Если книги не переданы, используем все
        if books is None:
            books = self.books

        # Заполняем таблицу
        for book in books:
            self.tree.insert("", "end", values=(
                book["title"],
                book["author"],
                book["genre"],
                book["pages"] if book["pages"] > 0 else "Не указано"
            ))

# Запуск приложения
if __name__ == "__main__":
    root = tk.Tk()
    app = BookTrackerApp(root)
    root.mainloop()
