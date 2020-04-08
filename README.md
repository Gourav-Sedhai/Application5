# Application5
#BookStore
#Frontend
"""
A program that stores this book information
Title, Author
Year, ISBN

User can:

View all records
Search an entry
Add an entry 
Update an entry
Delete
Close
"""
from tkinter import *
import backend

selected_tuple = None

def get_selected_row(event):
    try:
        global selected_tuple
        index=list1.curselection()[0]
        selected_tuple=list1.get(index)
        e1.delete(0, END)
        e1.insert(END, selected_tuple[1])
        e2.delete(0, END)
        e2.insert(END, selected_tuple[2])
        e3.delete(0, END)
        e3.insert(END, selected_tuple[3])
        e4.delete(0, END)
        e4.insert(END, selected_tuple[4])
    except IndexError:
        pass

def view_command():
    list1.delete(0, END)
    for row in backend.view():
        list1.insert(END, row)

def search_command():
    list1.delete(0, END)
    for row in backend.search(title_text.get(), author_text.get(), year_text.get(), isbn_text.get()):
        list1.insert(END, row)

def add_command():
    backend.insert(title_text.get(), author_text.get(), year_text.get(), isbn_text.get())
    list1.delete(0, END)
    list1.insert(END, (title_text.get(), author_text.get(), year_text.get(), isbn_text.get()))

def delete_command():
    backend.delete(selected_tuple[0])
    
def update_command():
    backend.update(selected_tuple[0],title_text.get(), author_text.get(), year_text.get(), isbn_text.get())

window=Tk()

window.wm_title("BookStore")

l1=Label(window, text="Title", bg="#00ffff", width=8, fg="red")
l1.grid(row=0, column=0)

l2=Label(window, text="Author", bg="#00ffff", width=8, fg="red")
l2.grid(row=0, column=2)

l3=Label(window, text="Year", bg="#00ffff", width=8, fg="red")
l3.grid(row=1, column=0)

l4=Label(window, text="ISBN", bg="#00ffff", width=8, fg="red")
l4.grid(row=1, column=2)

title_text=StringVar()
e1=Entry(window, textvariable=title_text, fg="red")
e1.grid(row=0, column=1)

author_text=StringVar()
e2=Entry(window, textvariable=author_text, fg="red")
e2.grid(row=0, column=3)

year_text=StringVar()
e3=Entry(window, textvariable=year_text, fg="red")
e3.grid(row=1, column=1)

isbn_text=StringVar()
e4=Entry(window, textvariable=isbn_text, fg="red")
e4.grid(row=1, column=3)

list1=Listbox(window, height=7, width=40, fg="red")
list1.grid(row=2, column=0, rowspan=6, columnspan=2)

sb1=Scrollbar(window)
sb1.grid(row=2, column=2, rowspan=6)

list1.configure(yscrollcommand=sb1.set)
sb1.configure(command=list1.yview)

list1.bind('<<ListboxSelect>>',get_selected_row)

b1=Button(window, text="View all", width=12, bg="#00ffff", fg="red", command=view_command)
b1.grid(row=2, column=3)

b2=Button(window, text="Search", width=12, bg="#00ffff", fg="red", command=search_command)
b2.grid(row=3, column=3)

b3=Button(window, text="Add", width=12, bg="#00ffff", fg="red", command=add_command)
b3.grid(row=4, column=3)

b4=Button(window, text="Update", width=12, bg="#00ffff", fg="red", command=update_command)
b4.grid(row=5, column=3)

b5=Button(window, text="Delete", width=12, bg="#00ffff", fg="red", command=delete_command)
b5.grid(row=6, column=3)

b6=Button(window, text="Close", width=12, bg="#00ffff", fg="red", command=window.destroy)
b6.grid(row=7, column=3)

window.mainloop()

#Backend
import sqlite3

def connect():
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("CREATE TABLE IF NOT EXISTS book (id INTEGER PRIMARY KEY, title text, author text, year integer, isbn integer)")
    conn.commit()
    conn.close()

def insert(title, author, year, isbn):
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("INSERT INTO book VALUES (NULL,?,?,?,?)",(title, author, year, isbn))
    conn.commit()
    conn.close()

def view():
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("SELECT * FROM book")
    rows=cur.fetchall()
    conn.close()
    return rows

def search(title="", author="", year="", isbn=""):
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("SELECT * FROM book WHERE title=? OR author=? OR year=? OR isbn=?", (title, author, year, isbn))
    rows=cur.fetchall()
    conn.close()
    return rows

def delete(id):
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("DELETE FROM book WHERE id=?",(id,))
    conn.commit()
    conn.close()

def update(id, title, author, year, isbn):
    conn=sqlite3.connect("books.db")
    cur=conn.cursor()
    cur.execute("UPDATE book SET title=?, author=?, year=?, isbn=? WHERE id=?",(title, author, year, isbn, id))
    conn.commit()
    conn.close()

connect()
# insert("The sun", "John Smith", 1999, 87687687)
# delete(3)
# update(5,"The Moon","John Smooth", 1917, 878979)
# print(view())
# print(search(author="John Smith"))
