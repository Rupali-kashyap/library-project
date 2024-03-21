# library-project
import sqlite3
connection =  sqlite3.connect("sql_dbm.db")

def new_stu(name, id, father_name, course, contact_no, city):
    cursor = connection.cursor()
    insert_query='''
    insert into students(stu_name, stu_id, f_name, course_n, p_no, city) values (?, ?, ?, ?, ?,?);
    '''
    cursor.execute(insert_query, (name, id, father_name, course, contact_no, city))
    connection.commit()

def view_all_stu():
    cursor = connection.cursor()
    select_query="select stu_name, stu_id, f_name, course_n, p_no, city from students"
    cursor.execute(select_query)
    rows = cursor.fetchall()
    for row in rows:
        print(row[0], row[1], row [2], row[3], row[4], row[5])
                
def reg_new_book(book_name, id, book_author, publisher, type, price):   
    cursor = connection.cursor()
    insert_query='''
    insert into books(book_name, book_id, book_author, book_publish, book_type, book_price) values (?, ?, ?, ?, ?, ?);
    '''
    cursor.execute(insert_query, (book_name, id, book_author, publisher, type, price))
    connection.commit()

def view_all_book():
    
    cursor = connection.cursor()
    select_query = "select book_name, book_id, book_author, book_publish, book_type, book_price from books;"
    cursor.execute(select_query)
    rows = cursor.fetchall()
    for row in rows:
        print(row[0], row[1], row [2], row[3], row[4], row[5])
                    
def check_book_isvalid(book_id):
    cursor = connection.cursor()
    select_query="select count(book_id) from books where book_id=?"
    cursor.execute(select_query, (book_id,))                          
    rows = cursor.fetchall()
    
    book_count = rows[0][0]
    cursor.close()
    
    if book_count ==1:
        return True
    else:
        return False

def check_book_already_issued(book_id):
    cursor = connection.cursor()
    select_query="select count(book_id) from book_issue where book_id=?"
    cursor.execute(select_query, (book_id,))                          
    rows = cursor.fetchall()
    book_count = rows[0][0]
    
    if book_count == 1:
        
        return True
    else:
        return False 

def check_book_detail(book_id):
    cursor = connection.cursor()
    select_query = '''select b.book_id, b.book_name, b.book_author,  b.book_price, 
    s.stu_name, s.stu_id, s.f_name, s.course_n, s.p_no, s.city
    from books b
    left join book_issue bi on b.book_id = bi.book_id
    left join students s on s.stu_id = bi.stu_id
    where b.book_id = ?'''
    cursor.execute(select_query,(book_id,))       
    rows = cursor.fetchall()
    for row in rows:
        print(row[0],row[1], row[2], row[3], row[4], row[5], row[6], row[7], row[8], row[9])
    

def check_student_isvalid(student_id):
    cursor = connection.cursor()
    select_query="select count(stu_id) from students where stu_id=?"
    cursor.execute(select_query, (student_id,))                          
    rows = cursor.fetchall()
    
    student_count = rows[0][0]
    cursor.close()
    
    if student_count == 1:
        return True
    else:
        return False 


def get_book_count(stu_id):
    cursor = connection.cursor()
    select_query="select count(book_id) from book_issue where stu_id=?"
    
    cursor.execute(select_query, (stu_id,))                          
    rows = cursor.fetchall()
    
    book_count = rows[0][0]
    cursor.close()
    return book_count


def issue_the_book(student_id, book_id):
    cursor  = connection.cursor()            
    
    insert_query = '''
    insert into book_issue(stu_id, book_id, issue_date) values (?, ?, datetime('now'));
    '''
    
    cursor.execute(insert_query, (student_id, book_id))
    connection.commit()
    cursor.close()
    
def show_issue_book():
    cursor = connection.cursor()
    select_query = "select stu_id, book_id, issue_date from book_issue "
            
    cursor.execute(select_query,)   
    rows = cursor.fetchall()
    for row in rows:
        print(row[0],row[1], row[2])
        
def check_stu_det(stu_id):              
     cursor = connection.cursor()
     
     select_query = '''select s.stu_id, s.stu_name, s.stu_id, s.f_name, s.course_n, s.p_no, s.city,
     b.book_id, b.book_name, b.book_author, b.book_price , bi.date
     
     from students s 
     left join book_issue bi on s.stu_id = bi.stu_id
     left join books b on b.book_id = bi.book_id
     whe
     re s.stu_id= ?'''
      
     cursor.execute(select_query,(stu_id,))       
     rows = cursor.fetchall()
     for row in rows:
        print(row[0],row[1], row[2], row[3], row[4], row[5], row[6], row[7], row[8], row[9], row[10])   
         
def return_book(book_id):
    cursor = connection.cursor()
    
    delete_query="delete from book_issue where book_id=?"   
    cursor.execute(delete_query,(book_id,)) 
    connection.commit()
    
def update_stu_detail(name, id, father_name, course, contact_no, city):
    cursor = connection.cursor()
    update_query = '''UPDATE students 
                      SET 
                          stu_name = ?,
                          f_name = ?,
                          course_n = ?,
                          p_no = ?,
                          city = ?
                      WHERE
                          stu_id = ?;
                   '''   
    cursor.execute(update_query, (name, father_name, course, contact_no, city, id))
    connection.commit()
    
    
print("-----------------------------------------")
print("      LIBRARY MANAGMENT SYSTEM           ")
print("-----------------------------------------")
print("Select Any Option")
print("1. Enroll a New Student")
print("2. Browse Registered Students")
print("3. Add New Book Information")
print("4. Borrow a Book")
print("5. View All Library Books")
print("6. Review Borrowed Book Details")
print("7. Lookup Student Information")
print("8. Return a Borrowed Book")
print("9. Examine Book Information")
print("10. Update student detail")
while True:
    choice = input("Enter choice : ")
    
    if choice in ('1', '2', '3', '4','5','6','7','8','9','10'):
        if(choice == "1"): # register new student
            name = input("Enter your full name : ")
            id = input ("Enter your id : ")
            father_name = input("Enter your father name : ")
            course = input("Enter your course name : ")

            contact_no = input("Enter your mobile no : ")
            city = input("enter your city: ")
            new_stu(name, id, father_name, course, contact_no, city)
            
           
        elif(choice=="2"):  # check all student 
           view_all_stu()
                
        elif(choice == "3"): # register new book details
            book_name = input("Enter book name : ")
            id = input("Enter book id : ")
            book_author = input("Enter book's author name : ")
            publisher = input("Enter publisher name : ")

            type = input("Enter which type of book : ")
            price = input("Enter price of book: ")
            reg_new_book(book_name, id, book_author, publisher, type, price)
            
        elif(choice=="4"): #issue book
            book_id = input("Enter student id : ")
            
            if check_student_isvalid(book_id) == False:
                print("Invalid Student Id")
                continue
            
            if get_book_count(book_id) >= 2:
                print("already 2 books are issued")
                continue
            
            
            book_id = input("enter book id : ")
            
            if check_book_isvalid(book_id) == 0:
                print("the book id you have have entered is invalid")
                continue
            
            if check_book_already_issued(book_id) == 1:
                print("this book is already issue to someone")
                continue
            
            issue_the_book(book_id, book_id)       
                
        
        elif(choice=="5"): #all books details
            view_all_book()
            
        elif(choice=="6"): #check which book is issue
            show_issue_book()
        

        elif(choice=="7"):  #check any student details
            book_id = input("enter student id: ") 
            check_stu_det(book_id)
            
        elif(choice=="8"): #delete any book details
            book_id = input("Enter book id: ")
            return_book(book_id) 
            
        elif(choice == "9"):  #check book details
            book_id = input("enter book id: ") 
            check_book_detail(book_id) 
        
        elif(choice== "10"):
             name = input("Enter your full name : ")
             id=input("enter id: ")
             father_name = input("Enter your father name : ")
             course = input("Enter your course name : ")
             contact_no = input("Enter your mobile no : ")
             city = input("enter your city: ")
             update_stu_detail(name, id, father_name, course, contact_no,  city)
     
        other_details = input("Do you want other details? (yes or no): ")
        if other_details== "no":
          break
    else:
        print("Invalid Input")
            
