#INTEC
#EXER4.1 (v8)

import tkinter as tk
from tkinter import messagebox
import csv
import os
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

#
# LATEST EDIT (EXER4)
lst = [['ID','Name', 'Address', 'Phone', 'Email', 'Birthday', 'Gender']]
ordlst = [['CID', 'PID', 'PType', 'PDesc', 'PSupplier', 'Quantity', 'Cost per unit', 'Total Cost', 'Date Ordered']]
lstindex=0
ordlstindex=0
globalrandomizer=0

# PRODUCTS WINDOW

prodquant = None
labelpquan = None
prodtoco = None
labelpcost = None
proddaterec = None
labelpdate = None

prodlst = [['ID','Type', 'Description', 'Supplier', 'Quantity', 'Cost', 'Date']]
lstindexp=0

def products():
    global creategrid2, prodquant, labelpquan, prodtoco, labelpcost, proddaterec, labelpdate
    windowprod = tk.Tk()
    windowprod.title("Product Information")
    windowprod.geometry("1400x500")
    windowprod.configure(bg='#F4DED3')

    label = tk.Label(windowprod, text="Product Information", width=30, height=1, bg="#F4DED3", anchor="center", fg='black')
    label.config(font=("Comic Sans MS", 14, "bold"))
    label.grid(column=2, row=1)

    def creategrid2(n):
        if n==0:
            for i in range(len(prodlst)):
                for j in range(len(prodlst[0])):
                    data = prodlst[i][j]
                    mgrid = tk.Entry(windowprod, width=15)
                    mgrid.insert(tk.END, data)
                    mgrid._values = mgrid.get(), i
                    mgrid.grid(row=i+14, column=j+8)
                    mgrid.bind("<Button-1>", callback2)
        if n==1:
            for label in windowprod.grid_slaves():
                if int(label.grid_info()["row"]) > 13:
                    label.grid_forget()

    
    def callback2(event):
        global lstindexp
        pli=[]
        pli=event.widget._values
        lstindexp = pli[1]
        prodid.configure(state=tk.NORMAL)
        prodid.delete(0,tk.END)
        prodid.insert(0, prodlst[pli[1]][0])
        prodtype.delete(0,tk.END)
        prodtype.insert(0, prodlst[pli[1]][1])
        proddesc.delete(0,tk.END)
        proddesc.insert(0, prodlst[pli[1]][2])
        prodsupp.delete(0,tk.END)
        prodsupp.insert(0, prodlst[pli[1]][3])
        prodquant.delete(0,tk.END)
        prodquant.insert(0, prodlst[pli[1]][4])
        prodtoco.delete(0,tk.END)
        prodtoco.insert(0, prodlst[pli[1]][5])
        proddaterec.delete(0,tk.END)
        proddaterec.insert(0, prodlst[pli[1]][6])
        prodid.configure(state=tk.DISABLED)
        labelpquan.config(text="  ")
        labelpcost.config(text="  ")
        labelpdate.config(text="Date is Valid", fg="black")
        lstindexp = pli[1]
        
    def savep():
        savevalid = msgbox("Save Data?", "Product Information Save")
        if savevalid == True:
            if (prodtype.get() == "" or proddesc.get() == "" or
                prodsupp.get() == "" or prodquant.get() == "" or
                prodtoco.get() == "" or proddaterec.get() == ""):
                messagebox.showinfo("Fail", "Save Faied, invalid information")
                return
            
            if (labelpquan.cget("text") != "  " or labelpcost.cget("text") != "  " or
                labelpdate.cget("text") != "Valid date"):
                messagebox.showinfo("Fail", "Save Faied, invalid information")
                return
    
        proddataloader()
    
        used_ids = {int(row[0]) for row in prodlst[1:]}
        new_id = 1
        while new_id in used_ids:
            new_id += 1
    
        new_product = [new_id, prodtype.get(), proddesc.get(), prodsupp.get(), prodquant.get(), prodtoco.get(), proddaterec.get()]
    
        prodlst.append(new_product)
        proddatawriter()
        creategrid2(1)
        creategrid2(0)

    def updatep():
        r=msgbox("Update Data?", "Product Information Update")
        if r == True:
            if (prodtype.get() == "" or proddesc.get() == "" or
                prodsupp.get() == "" or prodquant.get() == "" or
                prodtoco.get() == "" or proddaterec.get() == ""):
                messagebox.showinfo("Save Failed", "Product textbox is empty.")
                return
            
            if (labelpquan.cget("text") != "  " or labelpcost.cget("text") != "  " or
                labelpdate.cget("text") != "Valid date"):
                messagebox.showinfo("Save Failed", "Inputs are invalid.")
                return
            
            pidedit = prodlst[lstindexp][0]
            prodlst[lstindexp] = [pidedit, prodtype.get(), proddesc.get(), prodsupp.get(), prodquant.get(), prodtoco.get(), proddaterec.get()]
            proddatawriter()
            creategrid2(1)
            creategrid2(0)
    
    
    def erasep():
        r=msgbox("Delete Data?", "Information")
        if r == True:
            del(prodlst[lstindexp])
            proddatawriter()
            creategrid2(1)
            creategrid2(0)
    
    pidlabel = tk.Label(windowprod, text="ID:", width=15, height=1, bg="white")
    pidlabel.grid(column=1, row=2)

    pid = tk.StringVar()
    prodid = tk.Entry(windowprod, textvariable=pid)
    prodid.grid(column=2, row=2)
    prodid.configure(state=tk.DISABLED, disabledforeground="black", disabledbackground="#F4DED3")
    

    ptypelabel = tk.Label(windowprod, text="Type:", width=15, height=1, bg="white")
    ptypelabel.grid(column=1, row=3)

    ptype = tk.StringVar()
    prodtype = tk.Entry(windowprod, textvariable=ptype)
    prodtype.grid(column=2, row=3)


    pdesclabel = tk.Label(windowprod, text="Description:", width=15, height=1, bg="white")
    pdesclabel.grid(column=1, row=4)

    pdesc = tk.StringVar()
    proddesc = tk.Entry(windowprod, textvariable=pdesc)
    proddesc.grid(column=2, row=4)

    psupplierlabel = tk.Label(windowprod, text="Supplier:", width=15, height=1, bg="white")
    psupplierlabel.grid(column=1, row=5)

    psupp = tk.StringVar()
    prodsupp = tk.Entry(windowprod, textvariable=psupp)
    prodsupp.grid(column=2, row=5)

    prodquantity = tk.Label(windowprod, text="Quantity:", width=15, height=1, bg="white")
    prodquantity.grid(column=1, row=6)

    pquan = tk.StringVar()
    prodquant = tk.Entry(windowprod, textvariable=pquan)
    prodquant.grid(column=2, row=6)
    prodquant.bind("<KeyRelease>", lambda event: keyupPQuantity(event, labelpquan))

    labelpquan = tk.Label(windowprod, text="  ", width=15, height=1, bg="white", fg="black")
    labelpquan.grid(column=3, row=6)

    prodcost = tk.Label(windowprod, text="Cost:", width=15, height=1, bg="white")
    prodcost.grid(column=1, row=7)

    pcost = tk.StringVar()
    prodtoco = tk.Entry(windowprod, textvariable=pcost)
    prodtoco.grid(column=2, row=7)
    prodtoco.bind("<KeyRelease>", lambda event: keyupPCost(event, labelpcost))

    labelpcost = tk.Label(windowprod, text="  ", width=15, height=1, bg="white", fg="black")
    labelpcost.grid(column=3, row=7)

    proddate = tk.Label(windowprod, text="Date Recieved:", width=15, height=1, bg="white")
    proddate.grid(column=1, row=8)

    pdate = tk.StringVar()
    proddaterec = tk.Entry(windowprod, textvariable=pdate)
    proddaterec.grid(column=2, row=8)
    proddaterec.bind("<KeyRelease>", lambda event: keyupDateRecieved(event, labelpdate))

    labelpdate = tk.Label(windowprod, text="  ", width=15, height=1, bg="white", fg="black")
    labelpdate.grid(column=3, row=8)

    savepbutton = tk.Button(windowprod, text="Save", command=savep)
    savepbutton.grid(column=1, row=9)
    
    updatepbutton = tk.Button(windowprod, text="Update", command=updatep)
    updatepbutton.grid(column=2, row=9)
    
    erasepbutton = tk.Button(windowprod, text="Delete", command=erasep)
    erasepbutton.grid(column=3, row=9)
    
    creategrid2(0)

# PRODUCTS WINDOW ENDS HERE


def creategrid(n):
    for label in window.grid_slaves():
        if int(label.grid_info()["row"]) > 13:
            label.grid_forget()

    if n == 0:
        for i in range(len(lst)):
            for j in range(len(lst[0])):
                mgrid = tk.Entry(window, width=15)
                mgrid.insert(tk.END, lst[i][j])
                mgrid._values = mgrid.get(), i
                mgrid.grid(row=i + 14, column=j + 5)
                mgrid.bind("<Button-1>", callback)

    addorderbtn.grid(row=len(lst) + 14)
    delorderbtn.grid(row=len(lst) + 15)

    labels = ["Prod ID", "Type", "Desc", "Supp", "Qty", "Cost", "Total", "Date"]
    for labelindex, labeltext in enumerate(labels):
        label = tk.Label(window, text=labeltext, height=1, bg="#F4DED3", anchor="center", width=12)
        label.config(font=("Comic Sans MS", 9))
        label.grid(column=labelindex + 6, row=len(lst) + 17)


def callback(event):
    global lstindex
    li = event.widget._values
    lstindex = li[1]
    cust_id = lst[li[1]][0]
    custid.configure(state=tk.NORMAL)
    custbdate.configure(state=tk.NORMAL)
    cid.set(lst[li[1]][0]) 
    cname.set(lst[li[1]][1])
    cadress.set(lst[li[1]][2])
    cphone.set(lst[li[1]][3])
    cemail.set(lst[li[1]][4])
    cdate.set(lst[li[1]][5])
    cgender.set(lst[li[1]][6])
    custid.configure(state=tk.DISABLED)
    custbdate.configure(state=tk.DISABLED)
    labelname.config(text="Name is Valid")
    labelphone.config(text="Phone is Valid")
    labelemail.config(text="Email is Valid")
    labeldate.config(text="Date is Valid")
    ordergrid(cust_id)
    

#

def ordergrid(cust_id):
    for label in window.grid_slaves():
        if int(label.grid_info()["row"]) > 24:
            label.grid_forget()

    for i in range(len(ordlst)):
        if ordlst[i][0] == cust_id:
            for j in range(len(ordlst[0])):
                mgrid = tk.Entry(window, width=15)
                mgrid.insert(tk.END, ordlst[i][j])
                mgrid._values = mgrid.get(), i
                mgrid.grid(row=i + 25, column=j + 5)
                mgrid.bind("<Button-1>", ordercallback)
                	
def addorder(cust_id):
    global ordlst, prodlst, lstindexp

    prod_index = lstindexp
    prod_id = prodlst[prod_index][0]
    prod_type = prodlst[prod_index][1]
    prod_desc = prodlst[prod_index][2]
    prod_supplier = prodlst[prod_index][3]
    prod_price = int(prodlst[prod_index][5])
    prod_date = prodlst[prod_index][6]
    
    if int(prodlst[prod_index][4]) == 0:
        messagebox.showinfo("Order Cancelled", "This product is out of stock!")
        return
    
    existing_order_index = None
    for i, order in enumerate(ordlst):
        if order[0] == cust_id and order[1] == prod_id:
            existing_order_index = i
            break

    if existing_order_index is not None:
        ordlst[existing_order_index][5] = str(int(ordlst[existing_order_index][5]) + 1)
        ordlst[existing_order_index][7] = str(int(ordlst[existing_order_index][5]) * prod_price)
    else:
        ordlst.append([cust_id, prod_id, prod_type, prod_desc, prod_supplier, 1, prod_price, prod_price, prod_date])

    for index in range(len(prodlst)):
        product = prodlst[index]
        if product[0] == prod_id:
            product[4] = str(int(product[4]) - 1)
            break

    ordergrid(cust_id)
    orddatawriter()
    proddatawriter()
    creategrid2(1)
    creategrid2(0)

def delorder(cust_id):
    global ordlst, prodlst
    
    order_index = ordlstindex
    
    if order_index != -1 and order_index < len(ordlst):
        prod_id = ordlst[order_index][1]
        quantity = int(ordlst[order_index][5])

        if quantity > 1:
            ordlst[order_index][5] = str(quantity - 1)
            ordlst[order_index][7] = str((quantity - 1) * int(ordlst[order_index][6]))

            for product in prodlst:
                if product[0] == prod_id:
                    product[4] = str(int(product[4]) + 1)
                    break
        else:
            for product in prodlst:
                if product[0] == prod_id:
                    product[4] = str(int(product[4]) + 1)
                    break
            del ordlst[order_index]

    else:
        messagebox.showinfo("Order Delete", "Select a valid order to delete!")

    ordergrid(cust_id)
    orddatawriter()
    proddatawriter()
    creategrid2(1)
    creategrid2(0)


def ordercallback(event):
    global ordlstindex

    ordlstindex = -1
    for entry in window.grid_slaves():
        entry_row = int(entry.grid_info()["row"])
        if entry_row > 21:
            if entry == event.widget:
                ordlstindex = entry_row - 25
                print("Selected order index:", ordlstindex)
                break

#


custdatafilepath = 'C:\\Users\\Lenovo\\Documents\\CSV\\LenovoE9\\customers.csv'

proddatafilepath = 'C:\\Users\\Lenovo\\Documents\\CSV\\LenovoE9\\products.csv'

orddatafilepath = 'C:\\Users\\Lenovo\\Documents\\CSV\\LenovoE9\\orders.csv'

invoicefilepath = 'C:\\Users\\Lenovo\\Documents\\CSV\\LenovoE9\\invoice.pdf'

def custdatawriter():
    global lst
    with open(custdatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerows(lst)
    
def custdataloader():
    global lst
    try:
        with open(custdatafilepath, 'r', newline='') as csvfile:
            csvreader = csv.reader(csvfile)
            lst = list(csvreader)
    except FileNotFoundError:
        custdatacreate()

def custdatacreate():
    global lst
    header = ['ID', 'Name', 'Address', 'Phone', 'Email', 'Birthdate', 'Gender']
    with open(custdatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerow(header)
    lst = []
    
def proddatawriter():
    global prodlst
    with open(proddatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerows(prodlst)
    
def proddataloader():
    global prodlst
    try:
        with open(proddatafilepath, 'r', newline='') as csvfile:
            csvreader = csv.reader(csvfile)
            prodlst = list(csvreader)
    except FileNotFoundError:
        proddatacreate()

def proddatacreate():
    global prodlst
    header = ['ID','Type', 'Description', 'Supplier', 'Quantity', 'Cost', 'Date']
    with open(proddatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerow(header)
    prodlst = [] 
    
def orddatawriter():
    global ordlst
    with open(orddatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerows(ordlst)
    
def orddataloader():
    global ordlst
    try:
        with open(orddatafilepath, 'r', newline='') as csvfile:
            csvreader = csv.reader(csvfile)
            ordlst = list(csvreader)
    except FileNotFoundError:
        orddatacreate()

def orddatacreate():
    global ordlst
    header = ['CID', 'PID', 'PType', 'PDesc', 'PSupplier', 'Quantity', 'Cost', 'Total', 'Date Ordered']
    with open(orddatafilepath, 'w', newline='') as csvfile:
        csvwriter = csv.writer(csvfile)
        csvwriter.writerow(header)
    ordlst = []

def invoice():
    global ordlst, lst, prodlst, lstindex, lstindexp

    if lstindex == -1:
        messagebox.showinfo("Print Invoice", "Select a customer first.")
        return

    customer_id = lst[lstindex][0]
    customer_name = lst[lstindex][1]

    orders = [order for order in ordlst if order[0] == customer_id]
    
    orderdate = orders[0][8]
    for order in orders:
        if order[8] < orderdate:
            orderdate = order[8]

    if not orders:
        messagebox.showinfo("Print Invoice", "This customer has no orders.")
        return

    c = canvas.Canvas(invoicefilepath, pagesize=letter)
    
    c.setFont("Helvetica-Bold", 14)
    c.drawString(60, 750, "Customer Invoice | KOI ELECTRIC")
    
    c.setFont("Helvetica", 12)
    c.drawString(60, 710, "Koi Gee")
    c.drawString(60, 690, "Davao, Philippines")
    c.drawString(60, 670, f"Date issued: {orderdate}")
    
    c.drawString(325, 710, f"Customer ID# {cid.get()}")
    c.drawString(325, 690, f"Invoice# "+ str(100 + int(lst[lstindex][0])))
    c.drawString(325, 670, f"Contact us at 295-1234")
    
    c.line(50, 660, 550, 660)

    c.setFont("Helvetica-Bold", 14)
    c.drawString(60, 630, "Billed to:")

    c.setFont("Helvetica", 12)
    c.drawString(60, 610, f"{cname.get()}")
    c.drawString(60, 590, f"{cadress.get()}")
    c.drawString(60, 570, f"Date: {cdate.get()}")
    
    c.setFont("Helvetica", 12)
    c.drawString(325, 590, f"Email: {cemail.get()}")
    c.drawString(325, 570, f"Phone: {cphone.get()}")

    c.line(50, 560, 550, 560)
    
    c.setFont("Helvetica-Bold", 11)
    c.drawString(70, 530, "Product / Description")
    c.drawString(295, 530, "Supplier")
    c.drawString(407, 530, "QTY")
    c.drawString(515, 530, "Cost")
    
    c.line(50, 520, 550, 520)
    
    y = 510
    ordersubtotal = 0
    for order in orders:
        y -= 20
        c.drawString(65, y, f"{order[2]}")
        c.drawString(187, y, f"{order[3]}")
        c.drawString(290, y, f"{order[4]}")
        c.drawString(427, y, f"{order[5]}")
        c.drawString(515, y, f"P {order[6]}")
        unitprice = float(order[6])
        quantity = int(order[5])
        ordersubtotal += unitprice * quantity

    c.line(50, y - 10, 550, y - 10)
    
    ordertax = ordersubtotal * 0.12
    ordertotal = ordersubtotal + ordertax

    c.setFont("Helvetica-Bold", 12)
    c.drawString(440, y - 40, f"Sub Total: P {ordersubtotal:.2f}")
    c.drawString(446, y - 60, f"Tax 12%: P {ordertax:.2f}")
    c.drawString(465, y - 80, f"Total: P {ordertotal:.2f}")

    c.save()

    messagebox.showinfo("Create Invoice", f"Invoice has been created successfuly")
    os.startfile(invoicefilepath)

#

def save():
    global lst
    
    if (custnamecall.get() == "" or custadress.get() == "" or
        custphonenum.get() == "" or custemailcall.get() == "" or
        custbdate.get() == "" or custgender.get() == ""):
        messagebox.showinfo("Save Failed", "Customer textfields cannot be empty.")
        return

    if (labelname.cget("text") != "Valid Name" or labelphone.cget("text") != "Valid phone" or
            labelemail.cget("text") != "Valid Email" or labeldate.cget("text") != "Valid date"):
        messagebox.showinfo("Save Failed", "Customer inputs returned invalid.")
        return

    custdataloader()

    used_ids = {int(row[0]) for row in lst[1:]}
    new_id = 1
    while new_id in used_ids:
        new_id += 1

    new_customer = [new_id, custnamecall.get(), custadress.get(), custphonenum.get(),
                    custemailcall.get(), custbdate.get(), custgender.get()]

    lst.append(new_customer)
    custdatawriter()
    creategrid(0)


def update():
    r=msgbox("Update Data?", "Customer Information Update")
    if r == True:
        if (custnamecall.get() == "" or custadress.get() == "" or
            custphonenum.get() == "" or custemailcall.get() == "" or
            custbdate.get() == "" or custgender.get() == ""):
            messagebox.showinfo("Save Failed", "Customer textfields cannot be empty.")
            return

        if (labelname.cget("text") != "Valid Name" or labelphone.cget("text") != "Valid phone" or
            labelemail.cget("text") != "Valid Email" or labeldate.cget("text") != "Valid date"):
            messagebox.showinfo("Save Failed", "Customer inputs returned invalid.")
            return
        
        cidedit = lst[lstindex][0]
        lst[lstindex] =  [cidedit, custnamecall.get(), custadress.get(), custphonenum.get(), custemailcall.get(), custbdate.get(), custgender.get()]
        custdatawriter()
        creategrid(1)
        creategrid(0)

def erase():
    r=msgbox("Delete Data?", "Information")
    if r == True:
        del(lst[lstindex])
        custdatawriter()
        creategrid(1)
        creategrid(0)

#

def keyupPQuantity(event, label):
    global prodquant, labelpquan
    quantity = prodquant.get()
    isvalid = True

    for char in quantity:
        if not char.isdigit():
            isvalid = False
            break

    if isvalid:
        label.config(text="  ")
    else:
        label.config(text="Must be a number")

#

def keyupPCost(event, label):
    global prodtoco, labelpcost
    cost = prodtoco.get()
    isvalid = True

    for char in cost:
        if not char.isdigit():
            isvalid = False
            break

    if isvalid:
        label.config(text="  ")
    else:
        label.config(text="Must be a number")

#

def keyupDateRecieved(event, label):
    global proddaterec, labelpdate
    dateR = proddaterec.get()
    is_valid = True

    if len(dateR) != 10:
        is_valid = False
    else:
        if dateR[2] != '/' or dateR[5] != '/':
            is_valid = False

        for i in range(len(dateR)):
            if i != 2 and i != 5 and not dateR[i].isdigit():
                is_valid = False

    if is_valid:
        label.config(text="Valid date")
    else:
        label.config(text="Invalid date")

#

def msgbox(msg, titlebar):
    result = messagebox.askokcancel(title=titlebar, message=msg)
    return result

#

#

def customers():
    windowcust = tk.Toplevel(window)
    windowcust.title("Customer Center")
    windowcust.geometry("1400x500")
    windowcust.configure(bg='#F4DED3')

#

def keyupName(event, label):
	global custnamecall
	name = custnamecall.get()
	isvalid = False

	commaindex = name.find(',')
	spaceindex = name.find(' ', commaindex)
	dotindex = name.find(".")
	midintindex = name.find(' ', dotindex - 2)
	
	if commaindex > 0 and spaceindex == commaindex + 1:
 	   if dotindex > 0 and midintindex > 0:
 	       if name.endswith("."):
  	          isvalid = True

	if isvalid:
   	 label.config(text="Valid Name")
	else:
   	 label.config(text="Invalid Name")

#

def keyup(event, label):
    email = custemailcall.get()
    if "@" in email and "." in email and email.index("@") < email.index("."):
        at = True
    else:
        at = False

    if email.endswith(".com"):
        dotcom = True
    else:
        dotcom = False

    if at and dotcom:
        label.config(text="Mail is Valid")
    else:
        label.config(text="Invalid Email")

#

def keyupPhone(event, label):
    phone = custphonenum.get()
    isvalid = True

    if len(phone) != 12:
        isvalid = False
    else:
        if phone[4] != '-':
            isvalid = False

        for i in range(len(phone)):
            if i != 4 and not phone[i].isdigit():
                isvalid = False

    if isvalid:
        label.config(text="Phone is Valid")
    else:
        label.config(text="Invalid phone")

#

def keyupDate(event, label):
    date = custbdate.get()
    is_valid = True

    if len(date) != 10:
        is_valid = False
    else:
        if date[2] != '/' or date[5] != '/':
            is_valid = False

        for i in range(len(date)):
            if i != 2 and i != 5 and not date[i].isdigit():
                is_valid = False

    if is_valid:
        label.config(text="Date is Valid")
    else:
        label.config(text="Invalid date")

#

window = tk.Tk()
window.title("Customer Center")
window.geometry("1400x500")
window.configure(bg='#F4DED3')

menubar = tk.Menu(window)
filemenu = tk.Menu(menubar, tearoff=0)
menubar.add_cascade(label="File", menu=filemenu)
filemenu.add_command(label="Products", command=products)
filemenu.add_command(label="Customers", command=customers)
filemenu.add_separator()
filemenu.add_command(label="Close", command=window.quit)

window.config(menu=menubar)

#

label = tk.Label(window, text="Customer Center", width=30, height=1, bg="#F4DED3", anchor="center", fg="black")
label.config(font=("Comic Sans MS", 14, "bold"))
label.grid(column=2, row=1)

#

idlabel = tk.Label(window, text="ID:", width=15, height=1, bg="white", fg="black")
idlabel.grid(column=1, row=2)

cid = tk.StringVar()
custid = tk.Entry(window, textvariable=cid)
custid.grid(column=2, row=2)
custid.configure(state=tk.DISABLED, disabledforeground="black", disabledbackground="#F4DED3")

#

custname = tk.Label(window, text="Name:", width=15, height=1, bg="white", fg="black")
custname.grid(column=1, row=3)

cname = tk.StringVar()
custnamecall = tk.Entry(window, textvariable=cname, bg="white")
custnamecall.grid(column=2, row=3)
custnamecall.bind("<KeyRelease>", lambda event: keyupName(event, labelname))

labelname = tk.Label(window, text="Abc, Abc A.", width=15, height=1, bg="white", fg="black")
labelname.grid(column=3, row=3)

#

addresslabel = tk.Label(window, text="Address:", width=15, height=1, bg="white", fg="black")
addresslabel.grid(column=1, row=4)

cadress = tk.StringVar()
custadress = tk.Entry(window, textvariable=cadress, bg="white")
custadress.grid(column=2, row=4)

#

custphone = tk.Label(window, text="Phone:", width=15, height=1, bg="white", fg="black")
custphone.grid(column=1, row=5)

cphone = tk.StringVar()
custphonenum = tk.Entry(window, textvariable=cphone, bg="white")
custphonenum.grid(column=2, row=5)
custphonenum.bind("<KeyRelease>", lambda event: keyupPhone(event, labelphone))

labelphone = tk.Label(window, text="ex. 0991-12345678", width=15, height=1, bg="white", fg="black")
labelphone.grid(column=3, row=5)

#

custemail = tk.Label(window, text="Email:", width=15, height=1, bg="white", fg="black")
custemail.grid(column=1, row=6)

cemail = tk.StringVar()
custemailcall = tk.Entry(window, textvariable=cemail, bg="white")
custemailcall.grid(column=2, row=6)
custemailcall.bind("<KeyRelease>", lambda event: keyup(event, labelemail))

labelemail = tk.Label(window, text="abc@mail.com", width=15, height=1, bg="white", fg="black")
labelemail.grid(column=3, row=6)

#

custdate = tk.Label(window, text="Birthday:", width=15, height=1, bg="white", fg="black")
custdate.grid(column=1, row=7)

cdate = tk.StringVar()
custbdate = tk.Entry(window, textvariable=cdate, bg="white")
custbdate.grid(column=2, row=7)
custbdate.bind("<KeyRelease>", lambda event: keyupDate(event, labeldate))

labeldate = tk.Label(window, text="mm/dd/yyyy", width=15, height=1, bg="white", fg="black")
labeldate.grid(column=3, row=7)

#

genderlabel = tk.Label(window, text="Gender:", width=15, height=1, bg="white", fg="black")
genderlabel.grid(column=1, row=8)

cgender = tk.StringVar()
custgender = tk.Entry(window, textvariable=cgender, bg="white")
custgender.grid(column=2, row=8)

#

savebutton = tk.Button(window, text="Save", command=save)
savebutton.grid(column=1, row=11)

updatebutton = tk.Button(window, text="Update", command=update)
updatebutton.grid(column=2, row=11)

erasebutton = tk.Button(window, text="Delete", command=erase)
erasebutton.grid(column=3, row=11)

addorderbtn = tk.Button(text="Add this Order", command=lambda: addorder(lst[lstindex][0]))
addorderbtn.grid(column=2,row=15)

delorderbtn = tk.Button(text="Delete Order", command=lambda: delorder(lst[lstindex][0]))
delorderbtn.grid(column=2,row=16)

printinvoicebtn = tk.Button(text="Create Invoice", command=invoice)
printinvoicebtn.grid(column=3,row=12)

orddataloader()
proddataloader()
custdataloader()
creategrid(0)
window.mainloop()
