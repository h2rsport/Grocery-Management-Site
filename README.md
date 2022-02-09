# Grocery-Management-Site

import java.sql.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Scanner;

public class gms 
{
    // Variables for database connection
    
    private String dataconn = "jdbc:mariadb://localhost:3307/gms";
    private String user = "root";
    private String pass = "12345"; 
    
    Connection sqlconn = null;
    Statement stmt = null;
    ResultSet rs, rs1 = null;  
    
    // Till here
    
    // Function for database connection
    
    public void db_connection()
    {
        try
        {
            Class.forName("org.mariadb.jdbc.Driver");
            sqlconn = DriverManager.getConnection(dataconn, user, pass);
            stmt = sqlconn.createStatement();
        }
        catch(ClassNotFoundException | SQLException ex)
        {
            System.out.println("Exception : "+ex);
        }
    }
    
    //  Till here 
    
    // Function for add product
    
    public void add_product()
    {
        String product_name, company_name, product_type;
        int cost_per_unit;
        boolean flag;
        String ch;
        
        Scanner sc1 = new Scanner(System.in);
        Scanner sc2 = new Scanner(System.in);
        Scanner sc3 = new Scanner(System.in);
        Scanner sc4 = new Scanner(System.in);
        
        do
        {
            System.out.println("\n====================================================================");
            System.out.println("\n                      !!!   Add Product   !!!");
            System.out.println("\n====================================================================");
            
            do
            {
                System.out.print("\nEnter the product name : ");
                product_name = sc1.nextLine();

                flag = false;
                try
                {
                    rs = stmt.executeQuery("select product_name from product");   
                    while(rs.next())
                    {
                        if(product_name.equalsIgnoreCase(rs.getString("product_name")))
                        {
                            flag = true;
                            break;
                        }
                    }
                }
                catch(SQLException ex)
                {
                    System.out.println("Exception : "+ex);
                }

                if(flag==true)
                    System.out.println("\n!!!   Product is already available   !!!");
               
            }while(flag==true);
            
            System.out.print("\nEnter the cost per unit : ");
            cost_per_unit = sc2.nextInt();
            System.out.print("\nEnter the company name : ");
            company_name = sc3.nextLine();
            System.out.print("\nEnter the product type : ");
            product_type = sc4.nextLine();

            try
            {
                stmt.executeUpdate("insert into product(product_name,cost_per_unit,company_name,product_type) values('"+product_name+"',"+cost_per_unit+",'"+company_name+"','"+product_type+"');");
                System.out.println("\n====================================================================");
                System.out.println("\n              !!!   Product Added Successfully   !!!");
                System.out.println("\n====================================================================");
            }
            catch(SQLException ex)
            {
                System.out.println("Exception : "+ex);
            }
            
            flag = false;
            do
            {
                System.out.print("\nWant to add more product (y/n) : ");
                ch = sc2.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag!=true);
            
        }while(ch.equals("y"));
    }
    
    // Till here
    
    // Function for all product reports
    
    public void all_product_reports()
    {
        System.out.println("\n====================================================================");
        System.out.println("\n               !!!   All Product Reports   !!!");
        System.out.println("\n====================================================================");
        
        try
        {
            rs = stmt.executeQuery("select * from product");
                
            while(rs.next())
            {
                System.out.println("\nProduct id : "+rs.getInt("product_id"));
                System.out.println("Product name : "+rs.getString("product_name"));
                System.out.println("Cost Per Unit : "+rs.getInt("cost_per_unit"));
                System.out.println("Company Name : "+rs.getString("company_name"));
                System.out.println("Product Type : "+rs.getString("product_type"));
                System.out.println("\n---------------------------------------");
            }
        }    
        catch(SQLException ex)
        {
            System.out.println("Exception : "+ex);
        }
    }
    
    // Till here
    
    // Function for sales dashboard
    
    public void sales_dashboard()
    {
        String customer_name;
        int product_id;
        int quantity;
        long mobile_number;
        boolean flag;
        
        Scanner sc = new Scanner(System.in);
        
        System.out.println("\n====================================================================");
        System.out.println("\n                      !!!   Sales Dashboard   !!!");
        System.out.println("\n====================================================================");

        System.out.print("\nEnter the customer name : ");
        customer_name = sc.nextLine();
        
            String temp;
            boolean z;
            flag = false;
            do
            {
                int count = 0;
                
                z = false;

                System.out.print("\nEnter customer mobile number : ");
                temp = sc.nextLine();

                for(int i=0;i<temp.length();i++)
                {
                    if(Character.isDigit(temp.charAt(i)))
                        count++;
                }

                if(count==10)
                    flag = true;
                else
                    System.out.println("\n!!!   Please input a correct mobile number   !!!");

                if(flag==true)
                {
                    try
                    {
                        rs = stmt.executeQuery("select mobile_number from supplier");   

                        while(rs.next())
                        {
                            if(temp.equals(rs.getString("mobile_number")))
                            {
                                z = true;
                                break;
                            }
                        }
                    }
                    catch(SQLException ex)
                    {
                        System.out.println("Exception : "+ex);
                    }
                }

                if(z==true)
                {
                    flag = false;
                    System.out.println("\n!!!   Mobile number is already registered with a supplier   !!!");
                }

            }while(flag!=true);
            
            mobile_number = Long.parseLong(temp);
        
        do
        {
            System.out.print("\nEnter the product id : ");
            product_id = sc.nextInt();

            flag = false;
            try
            {
                rs = stmt.executeQuery("select product_id from product");   
                while(rs.next())
                {
                    if(rs.getInt("product_id")==product_id)
                    {
                        flag = true;
                        break;
                    }
                }
            }
            catch(SQLException ex)
            {
                System.out.println("Exception : "+ex);
            }

            if(flag==false)
            {
                System.out.println("\n!!!   Product name is not valid   !!!");
            }
        
    }while(flag!=true);
            
        System.out.print("\nEnter the quantity : ");
        quantity = sc.nextInt();

        try
        {
            DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");
            LocalDateTime now = LocalDateTime.now();            
            String date = dtf.format(now);
            
            stmt.executeUpdate("insert into sales values('"+customer_name+"',"+mobile_number+","+product_id+","+quantity+",'"+date+"');");
            System.out.println("\n====================================================================");
            System.out.println("\n                 !!!   Purchased Successfully   !!!");
            System.out.println("\n====================================================================");
        }
        catch(SQLException ex)
        {
            System.out.println("Exception : "+ex);
        }
    }
    
    // Till here
    
    // Function for sales reports
    
    public void sales_reports()
    {
        boolean flag = false;

        try
        {
            rs = stmt.executeQuery("select * from sales");
                
            while(rs.next())
            {
                if(flag==false)
                {
                    System.out.println("\n====================================================================");
                    System.out.println("\n                  !!!   Sales Reports   !!!");
                    System.out.println("\n====================================================================\n");
                }
                
                flag = true;
              
                rs1 = stmt.executeQuery("select * from product where product_id="+rs.getInt("product_id"));
                rs1.first();
                
                System.out.println(" @@   Product Detail   @@");
                
                System.out.println("\nProduct id : "+rs1.getInt("product_id"));
                System.out.println("Product name : "+rs1.getString("product_name"));
                System.out.println("Cost Per Unit : "+rs1.getInt("cost_per_unit"));
                System.out.println("Company Name : "+rs1.getString("company_name"));
                System.out.println("Product Type : "+rs1.getString("product_type"));
                
                System.out.println("\nQuantity of product : "+rs.getInt("quantity"));
                System.out.println("Purchaed Date : "+rs.getString("date"));
                System.out.println("Total Bill : "+rs1.getInt("cost_per_unit")*rs.getInt("quantity"));
                System.out.println("\n @@  Customer Detail  @@");
                System.out.println("Customer name : "+rs.getString("customer_name"));
                System.out.println("Customer mobile number : "+rs.getString("mobile_number"));
                
                System.out.println("\n---------------------------------------");
            }
        }    
        catch(SQLException ex)
        {
            System.out.println("Exception : "+ex);
        }
        
        if(flag==false)
        {
            System.out.println("\n====================================================================");
            System.out.println("\n                  !!!   No Sales Reports   !!!");
            System.out.println("\n====================================================================\n");
        }
    }
    
    // Till Here
    
    // Fucntion for suplier
    
    public void supplier()
    {
        int choice;
        String ch;
        boolean flag;
        
        Scanner sc = new Scanner(System.in);
        
        do
        {
            menu2();
            
            System.out.print("\nEnter your choice : ");
            choice = sc.nextInt();
            
            switch(choice)
            {
                case 1 -> add_supplier();
                case 2 -> delete_supplier();
                case 3 -> list_all_supplier();
                case 4 -> 
                {
                    System.out.println("\n====================================================================");
                    System.out.println("\n                  !!!   Successfully Exited   !!!");
                    System.out.println("\n====================================================================\n");
                    System.exit(0);
                }
            }
            
            flag = false;
            do
            {
                System.out.print("\nWant to go supplier menu(y/n) : ");
                ch = sc.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag!=true);
            
        }while(ch.equals("y"));
    }
    
    // Till here
    
    public void add_supplier()
    {
        String name, company_name;
        long mobile_number;
        boolean flag;
        String ch;
        
        Scanner sc = new Scanner(System.in);
        Scanner sc1 = new Scanner(System.in);
        
        do
        {
            System.out.println("\n====================================================================");
            System.out.println("\n                      !!!   Add Supplier   !!!");
            System.out.println("\n====================================================================");

            System.out.print("\nEnter supplier name : ");
            name = sc.nextLine();
            System.out.print("\nEnter supplier company name : ");
            company_name = sc.nextLine();
            
            String temp;
            boolean z;
            flag = false;
            do
            {
                int count = 0;
                
                z = false;

                System.out.print("\nEnter suplier mobile number : ");
                temp = sc.nextLine();

                for(int i=0;i<temp.length();i++)
                {
                    if(Character.isDigit(temp.charAt(i)))
                        count++;
                }

                if(count==10)
                    flag = true;
                else
                    System.out.println("\n!!!   Please input a correct mobile number   !!!");

                if(flag==true)
                {
                    try
                    {
                        rs = stmt.executeQuery("select mobile_number from supplier");   

                        while(rs.next())
                        {
                            if(temp.equals(rs.getString("mobile_number")))
                            {
                                z = true;
                                break;
                            }
                        }
                    }
                    catch(SQLException ex)
                    {
                        System.out.println("Exception : "+ex);
                    }
                }

                if(z==true)
                {
                    flag = false;
                    System.out.println("\n!!!   Mobile number is already registered with a supplier   !!!");
                }

            }while(flag!=true);
            
            mobile_number = Long.parseLong(temp);
            
            try
            {
                stmt.executeUpdate("insert into supplier(name,company_name,mobile_number) values('"+name+"','"+company_name+"',"+mobile_number+");");
                System.out.println("\n====================================================================");
                System.out.println("\n              !!!   Supplier Added Successfully   !!!");
                System.out.println("\n====================================================================");
            }
            catch(SQLException ex)
            {
                System.out.println("Exception : "+ex);
            }
            
            flag = false;
            do
            {
                System.out.print("\nWant to add more supplier (y/n) : ");
                ch = sc1.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag!=true);
            
        }while(ch.equals("y"));
    }
    
    // Till here
    
    // Function for list all supplier
    
    public void list_all_supplier()
    {
        System.out.println("\n====================================================================");
        System.out.println("\n               !!!   List All Supplier   !!!");
        System.out.println("\n====================================================================");
        
        try
        {
            rs = stmt.executeQuery("select * from supplier");
                
            while(rs.next())
            {
                System.out.println("\nSupplier Id : "+rs.getInt("supplier_id"));
                System.out.println("Supplier name : "+rs.getString("name"));
                System.out.println("Supplier company name : "+rs.getString("company_name"));
                System.out.println("Supplier mobile number : "+rs.getString("mobile_number"));
                System.out.println("\n---------------------------------------");
            }
        }    
        catch(SQLException ex)
        {
            System.out.println("Exception : "+ex);
        }
    }
    
    // Till here
    
    // Function for delete supplier
    
    public void delete_supplier()
    {
        boolean flag = false, flag1;
        String ch;
        int supplier_id;
        
        Scanner sc = new Scanner(System.in);
        Scanner sc1 = new Scanner(System.in);
        
        do
        {
            System.out.print("\nEnter supplier id : ");
            supplier_id  = sc.nextInt();

            try
            {
                rs = stmt.executeQuery("select supplier_id from supplier");

                while(rs.next())
                {
                    if(rs.getInt("supplier_id")==supplier_id)
                    {
                        flag = true;
                        stmt.executeUpdate("delete from supplier where supplier_id="+supplier_id);
                        System.out.println("\n====================================================================");
                        System.out.println("\n                 !!!   Supplier Deleted Successfully   !!!");
                        System.out.println("\n====================================================================");
                        break;
                    }
                }

                if(flag==false)
                {
                    System.out.println("\n====================================================================");
                    System.out.println("\n              !!!   Invalid Supplier Id   !!!");
                    System.out.println("\n====================================================================");
                }
            }    
            catch(SQLException ex)
            {
                System.out.println("Exception : "+ex);
            }
            
            flag1 = false;
            do
            {
                System.out.print("\nWant to delete more supplier (y/n) : ");
                ch = sc1.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag1 = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag1!=true);
            
        }while(ch.equals("y"));
    }
    
    // Till here
    
    // Function for delete product
    
    public void delete_product()
    {
        boolean flag = false, flag1;
        String ch;
        int product_id;
        
        Scanner sc = new Scanner(System.in);
        Scanner sc1 = new Scanner(System.in);
        
        do
        {
            System.out.print("\nEnter product id : ");
            product_id  = sc.nextInt();

            try
            {
                rs = stmt.executeQuery("select product_id from product");

                while(rs.next())
                {
                    if(rs.getInt("product_id")==product_id)
                    {
                        flag = true;
                        stmt.executeUpdate("delete from product where product_id="+product_id);
                        System.out.println("\n====================================================================");
                        System.out.println("\n                 !!!   Product Deleted Successfully   !!!");
                        System.out.println("\n====================================================================");
                        break;
                    }
                }

                if(flag==false)
                {
                    System.out.println("\n====================================================================");
                    System.out.println("\n                 !!!   Invalid Product Id   !!!");
                    System.out.println("\n====================================================================");
                }
            }    
            catch(SQLException ex)
            {
                System.out.println("Exception : "+ex);
            }
            
            flag1 = false;
            do
            {
                System.out.print("\nWant to delete more product (y/n) : ");
                ch = sc1.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag1 = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag1!=true);
            
        }while(ch.equals("y"));
    }
    
    // Till here
        
    // Function for menu 1
    
    public void menu1()
    {
        System.out.println();
        System.out.println("=========================================================");
        System.out.println("||     @@@         Grocery Management System     @@@   ||");
        System.out.println("=========================================================");
        System.out.println("||                                                     ||");
        System.out.println("||               1. Add Product                        ||");
        System.out.println("||                                                     ||");
        System.out.println("||               2. All Product Reports                ||");
        System.out.println("||                                                     ||");
        System.out.println("||               3. Sales Dashboard                    ||");
        System.out.println("||                                                     ||");
        System.out.println("||               4. Sales Reports                      ||");
        System.out.println("||                                                     ||");
        System.out.println("||               5. Supplier                           ||");
        System.out.println("||                                                     ||");
        System.out.println("||               6. Delete Product                     ||");
        System.out.println("||                                                     ||");
        System.out.println("||               7. Exit                               ||");
        System.out.println("||                                                     ||");
        System.out.println("=========================================================");
    }
    
    // Till here
    
    // Fucntion for Menu 2
    
    public void menu2()
    {
        System.out.println();
        System.out.println("============================================");
        System.out.println("||   @@@         Supplier         @@@     ||");
        System.out.println("============================================");
        System.out.println("||                                        ||");
        System.out.println("||            1. Add Supplier             ||");
        System.out.println("||                                        ||");
        System.out.println("||            2. Delete Supplier          ||");
        System.out.println("||                                        ||");
        System.out.println("||            3. List All Supplier        ||");
        System.out.println("||                                        ||");
        System.out.println("||            4. Exit                     ||");
        System.out.println("||                                        ||");
        System.out.println("============================================");
    }
    
    // Till here
    
    // Main Function
    
    public static void main(String[] args) 
    {
        gms obj = new gms();
        obj.db_connection();
        
        int choice;
        String ch;
        boolean flag;
        Scanner sc = new Scanner(System.in);
        
        do
        {
            obj.menu1();
            
            flag = false;
            do
            {
                System.out.print("\nEnter your choice : ");
                
                while(!sc.hasNextInt())
                {
                    System.out.print("\n!!!   Choice should be between 1 to 7   !!!");
                    System.out.print("\n\nEnter your choice : ");
                    sc.next();
                }  
                choice = sc.nextInt();
            
                if(choice>0 && choice<8)
                    flag = true;
                else
                    System.out.print("\n!!!   Choice should be between 1 to 7   !!!\n");
            
            }while(flag==false);
            
            switch(choice)
            {
                case 1 -> obj.add_product();
                case 2 -> obj.all_product_reports();
                case 3 -> obj.sales_dashboard();
                case 4 -> obj.sales_reports();
                case 5 -> obj.supplier();
                case 6 -> obj.delete_product();
                case 7 -> 
                {
                    System.out.println("\n====================================================================");
                    System.out.println("\n                  !!!   Successfully Exited   !!!");
                    System.out.println("\n====================================================================\n");
                    System.exit(0);
                }
            }
             
            flag = false;
            do
            {
                System.out.print("\nWant to go back to main menu (y/n) : ");
                ch = sc.next();
                
                if(ch.equals("y") || ch.equals("n"))
                    flag = true;
                else
                    System.out.print("\n!!!   Choice should be either y or n   !!!\n");
            
            }while(flag!=true);
                        
        }while(ch.equals("y"));            
    }
    
    // Till here
}
