# Medical-Management-System

## Project Description 
SQL Based project: used tkinter module for frontend, MySQL for backend(storage). It is a system for a medical it takes data for both Costomers and Medicines. 
Medicine-Part: It has 5 selection buttons for Add, View, Update, Delete and Back to Home Window. On cliking Add it goes to add slide where you can add new medicine by filling data of  it's id,name, availability/stock, and price. On clicking View you can check all the available medicines in SQL database table. On clicking Update you can update data of available medicine here you are allowed to change price of the medicine and add/update stock of medicine.On clicking Delete you can Delete the medicine from data.
Customer-Part:It has 5 selection buttons for Add, View, Update, Delete and Back to Home Window. On cliking Add it goes to add slide where you can add new customer entry by filling data of  it's id,name, contact, medicine name, quantity of buying and paid price, pending price; where paid price and pending price should add up to become total price of the medicine. On clicking View you can check all Customers data available in SQL database table. On clicking Update you can update data of customer here you are allowed to change paid price and pending price of the medicine by customer and contact number of customer.On clicking Delete you can Delete customer from data.

## Validations:
### Medicine
1-Id, Availability, Price Should be non negetive Integer(positive Integer) Only.
2-Id can not be same for two or more medicines.
### Customer
1-Id, Contact number, Quantity of buying, Paid Price, Pending Price Should be non negetive Integer(positive Integer) Only.
2-Id, Contact number can not be same for two or more Customers.
3-Customer can not buy medicine which is not available in stock/data.
4-Customer can not buy medicine more than it's availability in data.
5-Customer can not pay more or less amount for medicine that the original total ammount of medicine.

## Python Code:
```
from tkinter import *
from tkinter.messagebox import *
from tkinter.scrolledtext import *
from mysql.connector import *

def fw_cfw():
	fw.withdraw()
	cfw.deiconify()

def cfw_fw():
	cfw.withdraw()
	fw.deiconify()

def cfw_cad():
	cfw.withdraw()
	cad.deiconify()

def cad_cfw():
	cad.withdraw()
	cfw.deiconify()

def cad_enter():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
		try:
			c_id=int(cad_ent_id.get())
		except Exception:
			showerror("issue","Id should not be empty/non integer")
			return
		try:
			c_name=str(cad_ent_name.get())
		except Exception:
			showerror("issue","name should not be non alphabetical/empty")
			return
		c_contact=str(cad_ent_contact.get())
		try:
			med_name=str(cad_ent_med.get())
		except Exception:
			showerror("issue","medicine name should not be non alphabetical/empty")
			return
		try:
			quantity=int(cad_ent_qnt.get())
		except Exception:
			showerror("issue","quantity should not be empty/non integer")
			return
		try:
			paid=float(cad_ent_paid.get())
		except Exception:
			showerror("issue","paid should not be empty/non integer")
			return
		try:
			pending=float(cad_ent_pending.get())
		except Exception:
			showerror("issue","pending should not be empty/non integer")
			return

		cursor=con.cursor()
		sql_1="select check_price('%d','%s','%d','%f','%f')"
		cursor.execute(sql_1%(c_id,med_name,quantity,paid,pending))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="call cstmr_insert('%d','%s','%s','%s','%d','%f','%f')"
			cursor.execute(sql_2%(c_id,c_name,c_contact,med_name,quantity,paid,pending))
			con.commit()
			showinfo("success","record saved")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		cad_ent_id.delete(0,END)
		cad_ent_name.delete(0,END)
		cad_ent_contact.delete(0,END)
		cad_ent_med.delete(0,END)
		cad_ent_qnt.delete(0,END)
		cad_ent_paid.delete(0,END)
		cad_ent_pending.delete(0,END)
		cad_ent_id.focus()


def cfw_cvw():
	cfw.withdraw()
	cvw.deiconify()
	cvw_st_data.delete(1.0,END)
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
		cursor=con.cursor()
		sql="select * from customers"
		cursor.execute(sql)
		data=cursor.fetchall()
		info=""
		for d in data:
			info=info+"c_id= "+str(d[0])+" c_name= "+str(d[1])+" c_contact= "+str(d[2])+" med_name= "+str(d[3])+" quantity= "+str(d[4])+" paid= "+str(d[5])+" pending= "+str(d[6])+"\n"+("_"*50)+"\n"
		cvw_st_data.insert(INSERT,info)
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()

def cvw_search():
	cvw_st_data.delete(1.0,END)
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
		c_name=str(cvw_ent_name.get())
		cursor=con.cursor()
		sql_1="select cstmr_search('%s')"
		cursor.execute(sql_1%(c_name))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="select * from customers where c_name='%s'"
			cursor.execute(sql_2%(c_name))
			data=cursor.fetchall()
			info=""
			for d in data:
				info=info+"c_id= "+str(d[0])+" c_name= "+str(d[1])+" c_contact= "+str(d[2])+" med_name= "+str(d[3])+" quantity= "+str(d[4])+" paid= "+str(d[5])+" pending= "+str(d[6])+"\n"+("_"*50)+"\n"
			cvw_st_data.insert(INSERT,info)
		else:
			info="no data found.."
			cvw_st_data.insert(INSERT,info)
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		cvw_ent_name.delete(0,END)
		cvw_ent_name.focus()

def cvw_cfw():
	cvw.withdraw()
	cfw.deiconify()

def cfw_cud():
	cfw.withdraw()
	cud.deiconify()

def cud_update():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")

		try:
			c_id=int(cud_ent_id.get())
		except Exception:
			showerror("issue","Id should not be empty/non integer")
			return
		try:
			c_name=str(cud_ent_name.get())
		except Exception:
			showerror("issue","name should not be non alphabetical/empty")
			return
		c_contact=str(cud_ent_contact.get())
		try:
			med_name=str(cud_ent_med.get())
		except Exception:
			showerror("issue","medicine name should not be non alphabetical/empty")
			return
		try:
			quantity=int(cud_ent_qnt.get())
		except Exception:
			showerror("issue","quantity should not be empty/non integer")
			return
		try:
			paid=float(cud_ent_paid.get())
		except Exception:
			showerror("issue","paid should not be empty/non integer")
			return
		try:
			pending=float(cud_ent_pending.get())
		except Exception:
			showerror("issue","pending should not be empty/non integer")
			return

		cursor=con.cursor()
		sql_1="select check_udprice('%d','%s','%d','%f','%f')"
		cursor.execute(sql_1%(c_id,med_name,quantity,paid,pending))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="call cstmr_update('%d','%s','%s','%s','%d','%f','%f')"
			cursor.execute(sql_2%(c_id,c_name,c_contact,med_name,quantity,paid,pending))
			con.commit()
			showinfo("success","record updated")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		cud_ent_id.delete(0,END)
		cud_ent_name.delete(0,END)
		cud_ent_contact.delete(0,END)
		cud_ent_med.delete(0,END)
		cud_ent_qnt.delete(0,END)
		cud_ent_paid.delete(0,END)
		cud_ent_pending.delete(0,END)
		cud_ent_id.focus()

def cud_cfw():
	cud.withdraw()
	cfw.deiconify()

def cfw_cdl():
	cfw.withdraw()
	cdl.deiconify()

def cdl_delete():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
	
		try:
			c_id=int(cdl_ent_id.get())
		except Exception:
			showerror("issue","Id should not be empty/non integer")
			return

		cursor=con.cursor()
		sql_1="select cstmr_dlsearch('%d')"
		cursor.execute(sql_1%(c_id))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="delete from customers where c_id='%d'"
			cursor.execute(sql_2%(c_id))
			con.commit()
			showinfo("success","deleted data")
		else:
			showerror("issue","no data found..")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		cdl_ent_id.delete(0,END)
		cdl_ent_id.focus()

def cdl_cfw():
	cdl.withdraw()
	cfw.deiconify()

def fw_mfw():
	fw.withdraw()
	mfw.deiconify()

def mfw_fw():
	mfw.withdraw()
	fw.deiconify()

def mfw_mad():
	mfw.withdraw()
	mad.deiconify()

def mad_enter():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")

		try:
			m_id=int(mad_ent_id.get())
		except Exception:
			showerror("issue","Id should not be empty/non integer")
			return
		try:
			med_name=str(mad_ent_med.get())
		except Exception:
			showerror("issue","medicine name should not be empty")
			return
		try:
			availability=int(mad_ent_avbl.get())
		except Exception:
			showerror("issue","availability should not be empty/non integer")
			return
		try:
			price=float(mad_ent_price.get())
		except Exception:
			showerror("issue","price should not be empty/non integer")
			return

		cursor=con.cursor()
		sql="call med_insert('%d','%s','%d','%f')"
		cursor.execute(sql%(m_id,med_name,availability,price))
		con.commit()
		showinfo("success","record inserted")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		mad_ent_id.delete(0,END)
		mad_ent_med.delete(0,END)
		mad_ent_avbl.delete(0,END)
		mad_ent_price.delete(0,END)
		mad_ent_id.focus()

def mad_mfw():
	mad.withdraw()
	mfw.deiconify()

def mfw_mvw():
	mfw.withdraw()
	mvw.deiconify()
	mvw_st_data.delete(1.0,END)
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
		cursor=con.cursor()
		sql="select * from medicines"
		cursor.execute(sql)
		data=cursor.fetchall()
		info=""
		for d in data:
			info=info+"m_id= "+str(d[0])+" med_name= "+str(d[1])+" availability= "+str(d[2])+" price= "+str(d[3])+"\n"+("_"*50)+"\n"
		mvw_st_data.insert(INSERT,info)
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()

def mvw_search():
	mvw_st_data.delete(1.0,END)
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")
		med_name=str(mvw_ent_name.get())
		cursor=con.cursor()
		sql_1="select med_search('%s')"
		cursor.execute(sql_1%(med_name))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="select * from medicines where med_name='%s'"
			cursor.execute(sql_2%(med_name))
			data=cursor.fetchall()
			info=""
			for d in data:
				info=info+"m_id= "+str(d[0])+" med_name= "+str(d[1])+" availability= "+str(d[2])+" price= "+str(d[3])+"\n"+("_"*50)+"\n"
			mvw_st_data.insert(INSERT,info)
		else:
			info="no data found.."
			mvw_st_data.insert(INSERT,info)
	except Exception as e:
		if con is not None:
			con.rollback()
		print("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		mvw_ent_name.delete(0,END)
		mvw_ent_name.focus()

def mvw_mfw():
	mvw.withdraw()
	mfw.deiconify()

def mfw_mud():
	mfw.withdraw()
	mud.deiconify()

def mud_update():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")

		try:
			m_id=int(mud_ent_id.get())
		except Exception:
			showerror("issue","Id should not be empty/non integer")
			return
		try:
			med_name=str(mud_ent_med.get())
		except Exception:
			showerror("issue","medicine name should not be empty")
			return
		try:
			availability=int(mud_ent_avbl.get())
		except Exception:
			showerror("issue","availability should not be empty/non integer")
			return
		try:
			price=float(mud_ent_price.get())
		except Exception:
			showerror("issue","price should not be empty/non integer")
			return

		cursor=con.cursor()
		sql_2="call med_update('%d','%s','%d','%f')"
		cursor.execute(sql_2%(m_id,med_name,availability,price))
		con.commit()
		showinfo("success","record updated")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		mud_ent_id.delete(0,END)
		mud_ent_med.delete(0,END)
		mud_ent_avbl.delete(0,END)
		mud_ent_price.delete(0,END)
		mud_ent_id.focus()

def mud_mfw():
	mud.withdraw()
	mfw.deiconify()

def mfw_mdl():
	mfw.withdraw()
	mdl.deiconify()

def mdl_delete():
	con=None
	try:
		con=connect(host="localhost",user="root",password="abc123",database="db_medical")

		try:
			med_name=str(mdl_ent_med.get())
		except Exception:
			showerror("issue","medicine name should not be empty")
			return

		cursor=con.cursor()
		sql_1="select med_search('%s')"
		cursor.execute(sql_1%(med_name))
		check=cursor.fetchall()
		if check[0][0]==True:
			sql_2="delete from medicines where med_name='%s'"
			cursor.execute(sql_2%(med_name))
			con.commit()
			showinfo("success","deleted data")
		else:
			showerror("issue","no data found..")
	except Exception as e:
		if con is not None:
			con.rollback()
		showerror("issue : ",str(e))
	finally:
		if con is not None:
			con.close()
		mdl_ent_med.delete(0,END)
		mdl_ent_med.focus()

def mdl_mfw():
	mdl.withdraw()
	mfw.deiconify()

#first window
fw=Tk()

fw.iconbitmap("medicine_kart.ico")
bg_1=PhotoImage(file="ss1.PNG")
labell_1=Label(fw,image=bg_1)
labell_1.place(x=0,y=0)
fw.title("medical shop")
fw.geometry("1282x760+50+50")
frame_1=Frame(fw)
frame_1.pack(pady=20)
f=("Arial",30,"bold")

fw_btn_cstmr=Button(fw,text="Customers",font=f,width=10,command=fw_cfw)
fw_btn_med=Button(fw,text="Medicines",font=f,width=10,command=fw_mfw)

fw_btn_cstmr.pack(pady=20)
fw_btn_med.pack(pady=20)


#customers first window
cfw=Toplevel(fw)
cfw.iconbitmap("medicine_kart.ico")
bg_2=PhotoImage(file="ss1.PNG")
labell_2=Label(cfw,image=bg_2)
labell_2.place(x=0,y=0)
cfw.title("medical shop")
cfw.geometry("1282x760+50+50")
frame_2=Frame(cfw)
frame_2.pack(pady=20)

cfw_btn_add=Button(cfw,text="Add",font=f,width=10,command=cfw_cad)
cfw_btn_view=Button(cfw,text="View",font=f,width=10,command=cfw_cvw)
cfw_btn_update=Button(cfw,text="Update",font=f,width=10,command=cfw_cud)
cfw_btn_delete=Button(cfw,text="Delete",font=f,width=10,command=cfw_cdl)
cfw_btn_back=Button(cfw,text="Back",font=f,width=10,command=cfw_fw)

cfw_btn_add.pack(pady=10)
cfw_btn_view.pack(pady=10)
cfw_btn_update.pack(pady=10)
cfw_btn_delete.pack(pady=10)
cfw_btn_back.pack(pady=10)

cfw.withdraw()


#customers add
cad=Toplevel(fw)
cad.iconbitmap("medicine_kart.ico")
bg_2_1=PhotoImage(file="ss1.PNG")
labell_2_1=Label(cad,image=bg_2_1)
labell_2_1.place(x=0,y=0)
cad.title("medical shop")
cad.geometry("1282x760+50+50")
frame_2_1=Frame(cad)
frame_2_1.pack(pady=20)

cad_lab_id=Label(cad,text="Enter id",font=f,bg="grey13",fg="alice blue")
cad_ent_id=Entry(cad,font=f)

cad_lab_name=Label(cad,text="Enter name",font=f,bg="grey13",fg="alice blue")
cad_ent_name=Entry(cad,font=f)

cad_lab_contact=Label(cad,text="Enter contact number",font=f,bg="grey13",fg="alice blue")
cad_ent_contact=Entry(cad,font=f)

cad_lab_med=Label(cad,text="Enter medicine name",font=f,bg="grey13",fg="alice blue")
cad_ent_med=Entry(cad,font=f)

cad_lab_qnt=Label(cad,text="Enter quantity of buying",font=f,bg="grey13",fg="alice blue")
cad_ent_qnt=Entry(cad,font=f)

cad_lab_paid=Label(cad,text="Enter paid amount",font=f,bg="grey13",fg="alice blue")
cad_ent_paid=Entry(cad,font=f)

cad_lab_pending=Label(cad,text="Enter pending amount",font=f,bg="grey13",fg="alice blue")
cad_ent_pending=Entry(cad,font=f)

cad_btn_enter=Button(cad,text="Enter",font=f,width=10,bg="LightGoldenrod1",command=cad_enter)
cad_btn_back=Button(cad,text="Back",font=f,width=10,bg="LightGoldenrod1",command=cad_cfw)

cad_lab_id.place(x=60,y=60)
cad_ent_id.place(x=230,y=60)

cad_lab_name.place(x=60,y=120)
cad_ent_name.place(x=300,y=120)

cad_lab_contact.place(x=60,y=180)
cad_ent_contact.place(x=490,y=180)

cad_lab_med.place(x=60,y=240)
cad_ent_med.place(x=490,y=240)

cad_lab_qnt.place(x=60,y=300)
cad_ent_qnt.place(x=530,y=300)

cad_lab_paid.place(x=60,y=360)
cad_ent_paid.place(x=420,y=360)

cad_lab_pending.place(x=60,y=420)
cad_ent_pending.place(x=490,y=420)

cad_btn_enter.place(x=400,y=480)
cad_btn_back.place(x=400,y=570)

cad.withdraw()


#customers view
cvw=Toplevel(fw)
cvw.iconbitmap("medicine_kart.ico")
bg_2_2=PhotoImage(file="ss1.PNG")
labell_2_2=Label(cvw,image=bg_2_2)
labell_2_2.place(x=0,y=0)
cvw.title("medical shop")
cvw.geometry("1282x760+50+50")
frame_2_2=Frame(cvw)
frame_2_2.pack(pady=20)

cvw_lab_name=Label(cvw,text="search name",font=f,bg="grey13",fg="alice blue")
cvw_ent_name=Entry(cvw,font=f)
cvw_btn_search=Button(cvw,text="search",font=f,command=cvw_search)

cvw_st_data=ScrolledText(cvw,font=f,width=50,height=9)
cvw_btn_back=Button(cvw,text="Back",font=f,command=cvw_cfw)

cvw_lab_name.place(x=60,y=60)
cvw_ent_name.place(x=330,y=60)
cvw_btn_search.place(x=800,y=60)
cvw_st_data.pack(pady=120)
cvw_btn_back.place(x=500,y=600)

cvw.withdraw()


#customers update
cud=Toplevel(fw)
cud.iconbitmap("medicine_kart.ico")
bg_2_3=PhotoImage(file="ss1.PNG")
labell_2_3=Label(cud,image=bg_2_3)
labell_2_3.place(x=0,y=0)
cud.title("medical shop")
cud.geometry("1282x760+50+50")
frame_2_3=Frame(cud)
frame_2_3.pack(pady=20)

cud_lab_id=Label(cud,text="Enter id",font=f,bg="grey13",fg="alice blue")
cud_ent_id=Entry(cud,font=f)

cud_lab_name=Label(cud,text="Enter name",font=f,bg="grey13",fg="alice blue")
cud_ent_name=Entry(cud,font=f)

cud_lab_contact=Label(cud,text="Enter contact number",font=f,bg="grey13",fg="alice blue")
cud_ent_contact=Entry(cud,font=f)

cud_lab_med=Label(cud,text="Enter medicine name",font=f,bg="grey13",fg="alice blue")
cud_ent_med=Entry(cud,font=f)

cud_lab_qnt=Label(cud,text="Enter quantity of buying",font=f,bg="grey13",fg="alice blue")
cud_ent_qnt=Entry(cud,font=f)

cud_lab_paid=Label(cud,text="Enter paid amount",font=f,bg="grey13",fg="alice blue")
cud_ent_paid=Entry(cud,font=f)

cud_lab_pending=Label(cud,text="Enter pending amount",font=f,bg="grey13",fg="alice blue")
cud_ent_pending=Entry(cud,font=f)

cud_btn_update=Button(cud,text="Update",font=f,width=10,bg="LightGoldenrod1",command=cud_update)
cud_btn_back=Button(cud,text="Back",font=f,width=10,bg="LightGoldenrod1",command=cud_cfw)

cud_lab_id.place(x=60,y=60)
cud_ent_id.place(x=230,y=60)

cud_lab_name.place(x=60,y=120)
cud_ent_name.place(x=300,y=120)

cud_lab_contact.place(x=60,y=180)
cud_ent_contact.place(x=490,y=180)

cud_lab_med.place(x=60,y=240)
cud_ent_med.place(x=490,y=240)

cud_lab_qnt.place(x=60,y=300)
cud_ent_qnt.place(x=530,y=300)

cud_lab_paid.place(x=60,y=360)
cud_ent_paid.place(x=420,y=360)

cud_lab_pending.place(x=60,y=420)
cud_ent_pending.place(x=490,y=420)

cud_btn_update.place(x=400,y=480)
cud_btn_back.place(x=400,y=570)

cud.withdraw()


#customers delete
cdl=Toplevel(fw)
cdl.iconbitmap("medicine_kart.ico")
bg_2_4=PhotoImage(file="ss1.PNG")
labell_2_4=Label(cdl,image=bg_2_4)
labell_2_4.place(x=0,y=0)
cdl.title("medical shop")
cdl.geometry("1282x760+50+50")
frame_2_4=Frame(cdl)
frame_2_4.pack(pady=20)

cdl_lab_id=Label(cdl,text="Enter id",font=f,bg="grey13",fg="alice blue")
cdl_ent_id=Entry(cdl,font=f)

cdl_btn_delete=Button(cdl,text="Delete",font=f,width=10,bg="LightGoldenrod1",command=cdl_delete)
cdl_btn_back=Button(cdl,text="Back",font=f,width=10,bg="LightGoldenrod1",command=cdl_cfw)

cdl_lab_id.place(x=60,y=60)
cdl_ent_id.place(x=230,y=60)

cdl_btn_delete.place(x=400,y=180)
cdl_btn_back.place(x=400,y=270)

cdl.withdraw()


#medicines first window
mfw=Toplevel(fw)
mfw.iconbitmap("medicine_kart.ico")
bg_3=PhotoImage(file="ss1.PNG")
labell_3=Label(mfw,image=bg_3)
labell_3.place(x=0,y=0)
mfw.title("medical shop")
mfw.geometry("1282x760+50+50")
frame_3=Frame(mfw)
frame_3.pack(pady=20)

mfw_btn_add=Button(mfw,text="Add",font=f,width=10,command=mfw_mad)
mfw_btn_view=Button(mfw,text="View",font=f,width=10,command=mfw_mvw)
mfw_btn_update=Button(mfw,text="Update",font=f,width=10,command=mfw_mud)
mfw_btn_delete=Button(mfw,text="Delete",font=f,width=10,command=mfw_mdl)
mfw_btn_back=Button(mfw,text="Back",font=f,width=10,command=mfw_fw)

mfw_btn_add.pack(pady=10)
mfw_btn_view.pack(pady=10)
mfw_btn_update.pack(pady=10)
mfw_btn_delete.pack(pady=10)
mfw_btn_back.pack(pady=10)

mfw.withdraw()


#medicines add
mad=Toplevel(fw)
mad.iconbitmap("medicine_kart.ico")
bg_3_1=PhotoImage(file="ss1.PNG")
labell_3_1=Label(mad,image=bg_3_1)
labell_3_1.place(x=0,y=0)
mad.title("medical shop")
mad.geometry("1282x760+50+50")
frame_3_1=Frame(mad)
frame_3_1.pack(pady=20)

mad_lab_id=Label(mad,text="Enter id",font=f,bg="grey13",fg="alice blue")
mad_ent_id=Entry(mad,font=f)

mad_lab_med=Label(mad,text="Enter medicine name",font=f,bg="grey13",fg="alice blue")
mad_ent_med=Entry(mad,font=f)

mad_lab_avbl=Label(mad,text="Enter avilability ",font=f,bg="grey13",fg="alice blue")
mad_ent_avbl=Entry(mad,font=f)

mad_lab_price=Label(mad,text="Enter price",font=f,bg="grey13",fg="alice blue")
mad_ent_price=Entry(mad,font=f)

mad_btn_enter=Button(mad,text="Enter",font=f,width=10,bg="LightGoldenrod1",command=mad_enter)
mad_btn_back=Button(mad,text="Back",font=f,width=10,bg="LightGoldenrod1",command=mad_mfw)

mad_lab_id.place(x=60,y=60)
mad_ent_id.place(x=230,y=60)

mad_lab_med.place(x=60,y=120)
mad_ent_med.place(x=480,y=120)

mad_lab_avbl.place(x=60,y=180)
mad_ent_avbl.place(x=370,y=180)

mad_lab_price.place(x=60,y=240)
mad_ent_price.place(x=290,y=240)

mad_btn_enter.place(x=400,y=480)
mad_btn_back.place(x=400,y=570)

mad.withdraw()


#medicines view
mvw=Toplevel(fw)
mvw.iconbitmap("medicine_kart.ico")
bg_3_2=PhotoImage(file="ss1.PNG")
labell_3_2=Label(mvw,image=bg_3_2)
labell_3_2.place(x=0,y=0)
mvw.title("medical shop")
mvw.geometry("1282x760+50+50")
frame_3_2=Frame(mvw)
frame_3_2.pack(pady=20)

mvw_lab_name=Label(mvw,text="search medicine",font=f,bg="grey13",fg="alice blue")
mvw_ent_name=Entry(mvw,font=f)
mvw_btn_search=Button(mvw,text="search",font=f,command=mvw_search)

mvw_st_data=ScrolledText(mvw,font=f,width=50,height=9)
mvw_btn_back=Button(mvw,text="Back",font=f,command=mvw_mfw)

mvw_lab_name.place(x=60,y=60)
mvw_ent_name.place(x=380,y=60)
mvw_btn_search.place(x=850,y=60)
mvw_st_data.pack(pady=120)
mvw_btn_back.place(x=500,y=600)

mvw.withdraw()


#medicines update
mud=Toplevel(fw)
mud.iconbitmap("medicine_kart.ico")
bg_3_3=PhotoImage(file="ss1.PNG")
labell_3_3=Label(mud,image=bg_3_3)
labell_3_3.place(x=0,y=0)
mud.title("medical shop")
mud.geometry("1282x760+50+50")
frame_3_3=Frame(mud)
frame_3_3.pack(pady=20)

mud_lab_id=Label(mud,text="Enter id",font=f,bg="grey13",fg="alice blue")
mud_ent_id=Entry(mud,font=f)

mud_lab_med=Label(mud,text="Enter medicine name",font=f,bg="grey13",fg="alice blue")
mud_ent_med=Entry(mud,font=f)

mud_lab_avbl=Label(mud,text="Enter avilability ",font=f,bg="grey13",fg="alice blue")
mud_ent_avbl=Entry(mud,font=f)

mud_lab_price=Label(mud,text="Enter price",font=f,bg="grey13",fg="alice blue")
mud_ent_price=Entry(mud,font=f)

mud_btn_update=Button(mud,text="Update",font=f,width=10,bg="LightGoldenrod1",command=mud_update)
mud_btn_back=Button(mud,text="Back",font=f,width=10,bg="LightGoldenrod1",command=mud_mfw)

mud_lab_id.place(x=60,y=60)
mud_ent_id.place(x=230,y=60)

mud_lab_med.place(x=60,y=120)
mud_ent_med.place(x=480,y=120)

mud_lab_avbl.place(x=60,y=180)
mud_ent_avbl.place(x=370,y=180)

mud_lab_price.place(x=60,y=240)
mud_ent_price.place(x=290,y=240)

mud_btn_update.place(x=400,y=480)
mud_btn_back.place(x=400,y=570)

mud.withdraw()



#medicines delete
mdl=Toplevel(fw)
mdl.iconbitmap("medicine_kart.ico")
bg_3_4=PhotoImage(file="ss1.PNG")
labell_3_4=Label(mdl,image=bg_3_4)
labell_3_4.place(x=0,y=0)
mdl.title("medical shop")
mdl.geometry("1282x760+50+50")
frame_3_4=Frame(mdl)
frame_3_4.pack(pady=20)

mdl_lab_med=Label(mdl,text="Enter medicine name",font=f,bg="grey13",fg="alice blue")
mdl_ent_med=Entry(mdl,font=f)

mdl_btn_delete=Button(mdl,text="Delete",font=f,width=10,bg="LightGoldenrod1",command=mdl_delete)
mdl_btn_back=Button(mdl,text="Back",font=f,width=10,bg="LightGoldenrod1",command=mdl_mfw)

mdl_lab_med.place(x=60,y=120)
mdl_ent_med.place(x=480,y=120)

mdl_btn_delete.place(x=400,y=180)
mdl_btn_back.place(x=400,y=270)

mdl.withdraw()

fw.mainloop()

```

## Mysql Code:
### code for Database, Tabels, and Trigers:
```
drop database if exists db_medical;
create database if not exists db_medical;
use db_medical;

create table if not exists medicines
(
m_id int unsigned primary key auto_increment,
med_name varchar(100) unique not null,
availability int unsigned,
price float unsigned not null
);
desc medicines;


create table if not exists customers
(
c_id int unsigned primary key auto_increment,
c_name varchar(100) not null,
c_contact varchar(20) not null,
med_name varchar(100),
quantity int unsigned not null,
paid float unsigned,
pending float unsigned,
foreign key(med_name) references medicines(med_name) on delete set null
);
desc customers;


delimiter $$


drop trigger if exists med_t1 $$
create trigger med_t1 before insert on medicines for each row
begin
	if (new.m_id is null) or (new.m_id<1) then
		signal SQLSTATE '10101' set message_text="m_id can not be null or <1";
	end if;

	if (new.med_name is null) or (length(trim(new.med_name))<2) or (not new.med_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '10102' set message_text="med_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.availability<0) then
		signal SQLSTATE '10103' set message_text="availability can not be -ve";
	end if;

	if (new.price is null) or (new.price<1) then
		signal SQLSTATE '10104' set message_text="price can not be null or <1";
	end if;
end $$

drop trigger if exists med_t2 $$
create trigger med_t2 before update on medicines for each row
begin
	if (new.m_id is null) or (new.m_id<1) then
		signal SQLSTATE '10201' set message_text="m_id can not be null or <1";
	end if;

	if (new.med_name is null) or (length(trim(new.med_name))<2) or (not new.med_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '10202' set message_text="med_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.availability<0) then
		signal SQLSTATE '10203' set message_text="availability can not be -ve";
	end if;

	if (new.price is null) or (new.price<1) then
		signal SQLSTATE '10204' set message_text="price can not be null or <1";
	end if;
end $$


drop trigger if exists cstmr_t1 $$
create trigger cstmr_t1 before insert on customers for each row
begin
	if (new.c_id is null) or (new.c_id<1) then
		signal SQLSTATE '20101' set message_text="c_id can not be null or <1";
	end if;

	if (new.c_name is null) or (length(trim(new.c_name))<2) or (not new.c_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '20102' set message_text="c_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.c_contact is null) or (length(trim(new.c_contact))<10) or (length(trim(new.c_contact))>12) or (not new.c_contact regexp "^[0-9]+$") then
		signal SQLSTATE '20103' set message_text="invalid contact no.";
	end if;

	if (length(trim(new.med_name))<2) or (not new.med_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '20104' set message_text="med_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.quantity is null) or (new.quantity<1) then
		signal SQLSTATE '20105' set message_text="quantity can not be null or  <1";
	end if;

	if (new.paid<0) then
		signal SQLSTATE '20106' set message_text="paid can not be -ve";
	end if;

	if (new.pending<0) then
		signal SQLSTATE '20107' set message_text="pending can not be -ve";
	end if;

	if (new.paid is null) and (new.pending is null) then
		signal SQLSTATE '20108' set message_text="paid and pending can not be null at the same times";
	end if;

end $$

drop trigger if exists cstmr_t2 $$
create trigger cstmr_t2 before update on customers for each row
begin
	if (new.c_id is null) or (new.c_id<1) then
		signal SQLSTATE '20201' set message_text="c_id can not be null or <1";
	end if;

	if (new.c_name is null) or (length(trim(new.c_name))<2) or (not new.c_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '20202' set message_text="c_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.c_contact is null) or (length(trim(new.c_contact))<10) or (length(trim(new.c_contact))>12) or (not new.c_contact regexp "^[0-9]+$") then
		signal SQLSTATE '20203' set message_text="invalid contact no.";
	end if;

	if (length(trim(new.med_name))<2) or (not new.med_name regexp "^[A-Za-z ]+$") then
		signal SQLSTATE '20204' set message_text="med_name can not be null or blank or single letter and can not contain special characters or numbers";
	end if;

	if (new.quantity<1) then
		signal SQLSTATE '20205' set message_text="quantity can not be <1";
	end if;

	if (new.paid<0) then
		signal SQLSTATE '20206' set message_text="paid can not be -ve";
	end if;

	if (new.pending<0) then
		signal SQLSTATE '20207' set message_text="pending can not be -ve";
	end if;

	if (new.paid is null) and (new.pending is null) then
		signal SQLSTATE '20208' set message_text="paid and pending can not be null at the same time";
	end if;

end $$

delimiter ;

```
### code for insert medicine:
```
use db_medical;
delimiter $$

drop procedure if exists med_insert $$

create procedure if not exists med_insert(id int,name varchar(100),avbl int,pc decimal(10,2))
begin
declare co1 int default 0;
declare co2 int default 0; 
select count(*) into co1 from medicines where (m_id=id) or (med_name=name);
if co1=0 then
	insert into medicines values(id,name,avbl,pc);
else
	signal SQLSTATE '30002' set message_text="medicine id or medicine name already exists";
end if;
end $$

delimiter ;

```
### Update Medicine:
```
use db_medical;
delimiter $$

drop procedure if exists med_update $$

create procedure if not exists med_update(mi int,name varchar(100),avbl int,pc float)
begin 
	declare co int default 0;
if avbl>0 then
	select count(*) into co from medicines where (med_name=name) and (m_id=mi);
	if co!=0 then
		update medicines set availability=availability+avbl,price=pc where med_name=name;
	else
		signal SQLSTATE '30003' set message_text="wrong medicine id / medicine name";
	end if;
else 
	signal SQLSTATE '30003' set message_text="avability cannot be -ve";
end if;
end $$

delimiter ;

```
### Code for Search Medicine:
```
use db_medical;
delimiter $$

drop function if exists med_search $$
create function med_search(mn varchar(100)) returns boolean deterministic
begin
	declare ck boolean default false;
	declare co int default 0;
	select count(*) into co from medicines where med_name=mn;
	if co!=0 then
		set ck = true;
	end if;
return ck;
end $$

delimiter ;

```
### Customer Intsert Code:
```
use db_medical;
delimiter $$

drop procedure if exists cstmr_insert $$

create procedure if not exists cstmr_insert(id int,cnm varchar(100),cntc varchar(20),mnm varchar(100),qnt int,pd float,pen float)
begin 

	insert into customers values(id,cnm,cntc,mnm,qnt,pd,pen);
	update medicines set availability=availability-qnt where med_name=mnm;

end $$

delimiter ;

```
### Customer Update Code
```
use db_medical;
delimiter $$

drop procedure if exists cstmr_update $$

create procedure if not exists cstmr_update(id int,name varchar(100),cntct varchar(20),mn varchar(100),q int,pd float,pen float)
begin 
	declare co int default 0;
	select count(*) into co from customers where (c_id=id) and (med_name=mn) and (quantity=q);
	if co!=0 then
		if q>0 then
		update customers set c_name=name,c_contact=cntct,paid=pd,pending=pen where (c_id=id);
		else
			signal SQLSTATE '30002' set message_text="quantity can not be -ve";
		end if;
	else
		signal SQLSTATE '30002' set message_text="wrong customer id/medicine name/quantity";
	end if;
end $$

delimiter ;

```
### Code to Check Price in insert
```
use db_medical;

delimiter $$

drop function if exists check_price $$
create function check_price(id int,mn varchar(100),q int,pd float,pen float) returns boolean deterministic
begin
declare ck boolean default false;
declare pc float default 0.0;
declare co1 int default 0;
declare co2 int default 0;
declare co3 int default 0;
select count(*) into co1 from customers where c_id=id;
if co1=0 then
	select count(*) into co2 from medicines where (med_name=mn);
	if co2!=0 then
		select count(*) into co3 from medicines where (med_name=mn) and (availability>=q);
		if q>0 then
			if  co3>0 then
				select price into pc from medicines where med_name=mn;
				set pc=pc*q;
				if (pd=pc) or (pen=pc) or ((pd+pen)=pc) then
					set ck = true;
				else 
					signal SQLSTATE '40001' set message_text="wrong price inserted";
				end if;
			else 
				signal SQLSTATE '30001' set message_text="medicine out of stock";
			end if;
		else
			signal SQLSTATE '40002' set message_text="quantity cannot be -ve or zero";
		end if;
	else
		signal SQLSTATE '40002' set message_text="medicine does not exists";
	end if;
else
	signal SQLSTATE '40002' set message_text="id already exists";
end if;
return ck;
end $$

delimiter ;

```
### Code to Check Price in Update
```
use db_medical;

delimiter $$

drop function if exists check_udprice $$
create function check_udprice(id int,mn varchar(100),q int,pd float,pen float) returns boolean deterministic
begin
declare ck boolean default false;
declare pc float default 0.0;
declare co1 int default 0;
declare co2 int default 0;
declare co3 int default 0;
select count(*) into co1 from customers where c_id=id;
if co1!=0 then
	select count(*) into co2 from medicines where (med_name=mn);
	if co2!=0 then
		select count(*) into co3 from medicines where (med_name=mn) and (availability>=q);
		if q>0 then
			if  co3>0 then
				select price into pc from medicines where med_name=mn;
				set pc=pc*q;
				if (pd=pc) or (pen=pc) or ((pd+pen)=pc) then
					set ck = true;
				else 
					signal SQLSTATE '40001' set message_text="wrong price inserted";
				end if;
			else 
				signal SQLSTATE '30001' set message_text="medicine out of stock";
			end if;
		else
			signal SQLSTATE '40002' set message_text="quantity cannot be -ve or zero";
		end if;
	else
		signal SQLSTATE '40002' set message_text="medicine does not exists";
	end if;
else
	signal SQLSTATE '40002' set message_text="id does not exists";
end if;
return ck;
end $$

delimiter ;

```
### Customer Search In View
```
use db_medical;
delimiter $$

drop function if exists cstmr_search $$
create function cstmr_search(cn varchar(100)) returns boolean deterministic
begin
	declare ck boolean default false;
	declare co int default 0;
	select count(*) into co from customers where c_name=cn;
	if co!=0 then
		set ck = true;
	end if;
return ck;
end $$

delimiter ;

```


