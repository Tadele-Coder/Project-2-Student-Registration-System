# Project-2-Student-Registration-System
MY code class project 

import time
from tkinter import *
from PIL import ImageTk
import ttkthemes
from tkinter import ttk, messagebox, filedialog
import pymysql
import pandas
root = ttkthemes.ThemedTk()
root.get_themes()
root.set_theme('radiance')
root.resizable(0, 0)

count=0
text=''

def iexit():
   result = messagebox.askyesno('Confirm', 'Do you want to exit?')
   if result:
       root.destroy()

def export_data():
    url = filedialog.asksaveasfilename(defaultextension='.csv')
    indexing = studenttv.get_children()
    newlist = []

    for index in indexing:
        content = studenttv.item(index)
        datalist = content['values']
        newlist.append(datalist)

    table = pandas.DataFrame(newlist, columns=['Id', 'Name', 'Mobile No', 'Email', 'Address', 'Gender', 'DOB', 'Added Date', 'Added Time'])
    table.to_csv(url, index=False)
    messagebox.showinfo('Success', 'Data is saved Successfully')



def toplevel_data(title, button_text, command):

    global identry,nameentry,mobileentry,emailentry,addressentry,dobentry,genderentry,screen
    screen = Toplevel()
    screen.title(title)
    screen.grab_set()
    screen.resizable(0, 0)

    idlevel = Label(screen, text='Id', font=('times new roman', 20, 'bold'))
    idlevel.grid(row=0, column=0, padx=30, pady=15, sticky=W)
    identry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    identry.grid(row=0, column=1, padx=10, pady=10)

    namelevel = Label(screen, text='Name', font=('times new roman', 20, 'bold'))
    namelevel.grid(row=1, column=0, padx=30, pady=15, sticky=W)
    nameentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    nameentry.grid(row=1, column=1, padx=10, pady=10)

    mobilelevel = Label(screen, text='Mobile', font=('times new roman', 20, 'bold'))
    mobilelevel.grid(row=2, column=0, padx=30, pady=15, sticky=W)
    mobileentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    mobileentry.grid(row=2, column=1, padx=10, pady=10)

    emaillevel = Label(screen, text='Email', font=('times new roman', 20, 'bold'))
    emaillevel.grid(row=3, column=0, padx=30, pady=15, sticky=W)
    emailentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    emailentry.grid(row=3, column=1, padx=10, pady=10)

    addresslevel = Label(screen, text='Address', font=('times new roman', 20, 'bold'))
    addresslevel.grid(row=4, column=0, padx=30, pady=15, sticky=W)
    addressentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    addressentry.grid(row=4, column=1, padx=10, pady=10)

    genderlevel = Label(screen, text='Gender', font=('times new roman', 20, 'bold'))
    genderlevel.grid(row=5, column=0, padx=30, pady=15, sticky=W)
    genderentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    genderentry.grid(row=5, column=1, padx=10, pady=10)

    doblevel = Label(screen, text='DOB', font=('times new roman', 20, 'bold'))
    doblevel.grid(row=6, column=0, padx=30, pady=15, sticky=W)
    dobentry = Entry(screen, font=('times new roman', 15, 'bold'), width=24)
    dobentry.grid(row=6, column=1, padx=10, pady=10)

    studentbtn = ttk.Button(screen, text= button_text, command=command)
    studentbtn.grid(row=7, columnspan=2, padx=30, pady=15)

    if title=='Update Student':

        indexing = studenttv.focus()
        content = studenttv.item(indexing)
        datalist = content['values']
        identry.insert(0, datalist[0])
        nameentry.insert(0, datalist[1])
        mobileentry.insert(0, datalist[2])
        emailentry.insert(0, datalist[3])
        addressentry.insert(0, datalist[4])
        genderentry.insert(0, datalist[5])
        dobentry.insert(0, datalist[6])
def update():
    con = pymysql.connect(host='localhost', user='root', password='root', database='project')
    mycursor = con.cursor()
    currentdate = time.strftime('%d/%m/%Y')
    currenttime = time.strftime('%H:%M:%S')
    query = 'update student set Name=%s, Mobile=%s, Email=%s,Address=%s, Gender=%s, DOB=%s, Date=%s, Time=%s where Id=%s '
    mycursor.execute(query, (nameentry.get(), mobileentry.get(), emailentry.get(), addressentry.get(), genderentry.get(),dobentry.get(),currentdate, currenttime, identry.get()))
    con.commit()
    messagebox.showinfo('Success', 'Data has been Updated', parent=screen)
    screen.destroy()
    show_student()

def show_student():
    con = pymysql.connect(host='localhost', user='root', password='root', database='project')
    mycursor = con.cursor()
    query = 'select * from student'
    mycursor.execute(query)
    fetch_data = mycursor.fetchall()
    studenttv.delete(*studenttv.get_children())
    for data in fetch_data:
        studenttv.insert('', END, values=data)


def delete_student():
    con = pymysql.connect(host='localhost', user='root', password='root', database='project')
    mycursor = con.cursor()

    indexing = studenttv.focus()
    content = studenttv.item(indexing)
    content_id = content['values'][0]
    query = 'delete from student where Id=%s'
    mycursor.execute(query, (content_id))
    con.commit()
    messagebox.showinfo('Deleted', f' Id {content_id} has been deleted Successfuly')

    query = 'select * from student'
    mycursor.execute(query)
    fetch_data = mycursor.fetchall()
    studenttv.delete(*studenttv.get_children())
    for data in fetch_data:
        studenttv.insert('', END, values=data)
def search():
    con = pymysql.connect(host='localhost', user='root', password='root', database='project')
    mycursor = con.cursor()
    query = 'select * from student where Id=%s or name=%s or mobile=%s or email=%s or address=%s or gender=%s or dob=%s '
    mycursor.execute(query, (identry.get(), nameentry.get(), mobileentry.get(), emailentry.get(), addressentry.get(),genderentry.get(),dobentry.get()))
    studenttv.delete(*studenttv.get_children())
    fetch_data = mycursor.fetchall()
    for data in fetch_data:
        studenttv.insert('', END, values=data)
def add_data():

    if identry.get()=='' or nameentry.get()=='' or mobileentry.get()=='' or emailentry.get()=='' or addressentry.get()==''or genderentry.get()=='' or dobentry.get()=='':
        messagebox.showerror('Error', 'All Fields are required')
    else:
        currentdate = time.strftime('%d/%m/%Y')
        currenttime = time.strftime('%H:%M:%S')
        try:
            con = pymysql.connect(host='localhost', user='root', password='root', database='project')
            mycursor = con.cursor()

            query = 'insert into student values(%s,%s,%s,%s,%s,%s,%s,%s,%s)'
            mycursor.execute(query, (identry.get(), nameentry.get(),mobileentry.get(),emailentry.get(),addressentry.get(), genderentry.get(), dobentry.get(),currentdate,currenttime))
            con.commit()
            result = messagebox.askyesno('Confirm', 'Data added Successful, Do you want clean the form?')
            if result:
                identry.delete(0, END)
                nameentry.delete(0, END)
                mobileentry.delete(0, END)
                emailentry.delete(0, END)
                addressentry.delete(0, END)
                genderentry.delete(0, END)
                dobentry.delete(0, END)
                identry.focus()
        except:
            messagebox.showerror('Error', 'Id cannot be repeated', parent=add_window)
            return
        query = 'select * from student'
        mycursor.execute(query)
        fetch_data = mycursor.fetchall()
        studenttv.delete(*studenttv.get_children())

        for data in fetch_data:
            datalist = list(data)
            studenttv.insert('', END, values=datalist)

def connect_database():

    def connect():

        try:
            con = pymysql.connect(host='localhost', user= 'root', password='root', database='project')
            mycursor = con.cursor()
            messagebox.showinfo('Success', 'Database Connection is Successful', parent = connectwindow)
            connectwindow.destroy()

        except:
            messagebox.showerror('Error', 'Invalid details', parent = connectwindow)

        addbtn.config(state=NORMAL)
        searchbtn.config(state=NORMAL)
        deletebtn.config(state=NORMAL)
        updatebtn.config(state=NORMAL)
        showbtn.config(state=NORMAL)
        exportbtn.config(state=NORMAL)

    connectwindow = Toplevel()
    connectwindow.grab_set()
    connectwindow.geometry('470x250+730+230')
    connectwindow.title('Database Connection')
    connectwindow.resizable(0, 0)

    hostlabel = Label(connectwindow, text='Host Name', font=('arial', 20, 'bold'))
    hostlabel.grid(row=0, column=0, padx=20)

    hostnameentery = Entry(connectwindow, font=('arial', 15, 'bold'), bd=2)
    hostnameentery.grid(row=0, column=1, padx=20, pady=20)

    usernamelabel = Label(connectwindow, text='User Name', font=('arial', 20, 'bold'))
    usernamelabel.grid(row=1, column=0, padx=20)

    usernamenameentery = Entry(connectwindow, font=('arial', 15, 'bold'), bd=2)
    usernamenameentery.grid(row=1, column=1, padx=20, pady=20)

    passwordlabel = Label(connectwindow, text='Password', font=('arial', 20, 'bold'))
    passwordlabel.grid(row=2, column=0, padx=20)

    passwordnameentery = Entry(connectwindow, font=('arial', 15, 'bold'), bd=2)
    passwordnameentery.grid(row=2, column=1, padx=20, pady=20)

    connectbtn = ttk.Button(connectwindow, text='Connect', command=connect)
    connectbtn.grid(row=3, columnspan=2)


def slider():
    global text, count
    if count==len(s):
        count=0
        text=''
    text=text+s[count]
    count+=1
    sliderlabel.config(text=text)
    sliderlabel.after(300, slider)

def clock():
    currentdate = time.strftime('%d/%m/%Y')
    currenttime = time.strftime('%H:%M:%S')
    datetimelabel.config(text=f'   Date: {currentdate}\n Time: { currenttime}')
    datetimelabel.after(1000, clock)

root.geometry('1174x680+50+20')
root.title('Student Management System')
root.resizable(0, 0)



datetimelabel = Label(root, font=('times new roman', 18, 'bold'))
datetimelabel.place(x=5, y=5)
clock()
s= 'Student Management System'
sliderlabel = Label(root, text=s, font=('arial', 28, 'italic bold'), fg='red2', width=30)
sliderlabel.place(x=200, y=0)
slider()



database_connectbtn = ttk.Button(root, text='Connect To Database', command=connect_database)
database_connectbtn.place(x=920, y=5)

leftframe = Frame(root,)
leftframe.place(x=50, y=80, width=300, height=600)

logo_image = PhotoImage(file='student.png')
logo_imagelabel = Label(leftframe, image=logo_image)
logo_imagelabel.grid(row=0, column=0)


addbtn = ttk.Button(leftframe, text='Add Student', width=20, state=DISABLED, command=lambda: toplevel_data('Add Student', 'Add ', add_data))
addbtn.grid(row=1, column=0, pady=20)

searchbtn = ttk.Button(leftframe, text='Search Student', width=20, state=DISABLED, command=lambda: toplevel_data('Search Student', 'Search', search))
searchbtn.grid(row=2, column=0, pady=20)

deletebtn = ttk.Button(leftframe, text='Delete Student', width=20, state=DISABLED, command=delete_student)
deletebtn.grid(row=3, column=0, pady=20)

updatebtn = ttk.Button(leftframe, text='Update Student', width=20, state=DISABLED, command=lambda: toplevel_data('Update Student', 'Update Student', update))
updatebtn.grid(row=4, column=0, pady=20)

showbtn = ttk.Button(leftframe, text='Show Student', width=20, state=DISABLED, command=show_student)
showbtn.grid(row=5, column=0, pady=20)

exportbtn = ttk.Button(leftframe, text='Export Student', width=20, state=DISABLED, command=export_data)
exportbtn.grid(row=6, column=0, pady=20)

exitbtn = ttk.Button(leftframe, text='Exit', width=20, command=iexit)
exitbtn.grid(row=7, column=0, pady=15)

rightframe = Frame(root, bg='yellow')
rightframe.place(x=350, y=80, width=820, height=600)

xscrollbar = Scrollbar(rightframe, orient=HORIZONTAL)
xscrollbar.pack(side=BOTTOM, fill=X)

yscrollbar = Scrollbar(rightframe, orient=VERTICAL)
yscrollbar.pack(side=RIGHT, fill=Y)


studenttv = ttk.Treeview(rightframe, columns=('Id', 'Name', 'Mobile No', 'Email', 'Address', 'Gender',
                                              'DOB', 'Added Date', 'Added Time'), xscrollcommand=xscrollbar.set, yscrollcommand=yscrollbar.set)
studenttv.pack(fill=BOTH, expand=1)

xscrollbar.config(command=studenttv.xview)
yscrollbar.config(command=studenttv.yview)

studenttv.heading('Id', text='Id')
studenttv.heading('Name', text='Name')
studenttv.heading('Mobile No', text='Mobile No')
studenttv.heading('Email', text = 'Email')
studenttv.heading('Address', text='Address')
studenttv.heading('Gender', text='Gender')
studenttv.heading('DOB', text='DOB')
studenttv.heading('Added Date', text='Added Date')
studenttv.heading('Added Time', text='Added Time')

studenttv.column('Id', width=50, anchor=CENTER)
studenttv.column('Name', width=300, anchor=CENTER)
studenttv.column('Email', width=350, anchor=CENTER)
studenttv.column('Mobile No', width=200, anchor=CENTER)
studenttv.column('Address', width=400, anchor=CENTER)
studenttv.column('Gender', width=100, anchor=CENTER)
studenttv.column('DOB', width=200, anchor=CENTER)
studenttv.column('Added Date', width=200, anchor=CENTER)
studenttv.column('Added Time', width=200, anchor=CENTER)
studenttv.config(show='headings')

root.mainloop()
