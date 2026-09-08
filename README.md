1. index.jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Matrimonial Registration</title>

    <style>
        body {
            font-family: Arial, sans-serif;
            background-image: url('images/candles.jpg');
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            margin: 0;
            padding: 0;
        }

        form {
            background-color: rgba(255, 255, 255, 0.9);
            max-width: 600px;
            margin: 40px auto;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.2);
        }

        input[type="text"],
        input[type="password"],
        input[type="number"],
        select {
            width: 100%;
            padding: 8px 10px;
            margin-bottom: 18px;
            border: 1px solid #d6336c;
            border-radius: 4px;
            font-size: 14px;
            box-sizing: border-box;
        }

        label {
            font-weight: 600;
            color: #a02248;
            display: block;
            margin-bottom: 6px;
        }

        .gender-group {
            margin-bottom: 18px;
            color: #a02248;
        }

        input[type="radio"] {
            margin-right: 5px;
        }

        input[type="submit"] {
            width: 100%;
            background-color: #d6336c;
            border: none;
            color: white;
            font-weight: 700;
            padding: 10px 0;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }

        input[type="submit"]:hover {
            background-color: #66132c;
        }
    </style>
</head>

<body>

<form action="regForm" method="post">

    <label for="name1">Name:</label>
    <input type="text" id="name1" name="name1" required>

    <label for="email1">Email:</label>
    <input type="text" id="email1" name="email1" required>

    <label for="pass1">Password:</label>
    <input type="password" id="pass1" name="pass1" required>

    <label>Gender:</label>

    <div class="gender-group">
        <input type="radio" id="male" name="gender1" value="Male" required>
        <label for="male" style="display:inline;">Male</label>

        <input type="radio" id="female" name="gender1" value="Female" required>
        <label for="female" style="display:inline;">Female</label>
    </div>

    <label for="age">Age:</label>
    <input type="number" id="age" name="age" min="18" max="99" required>

    <label for="religion">Religion:</label>
    <select id="religion" name="religion" required>
        <option value="">Select religion</option>
        <option value="Hindu">Hindu</option>
        <option value="Muslim">Muslim</option>
        <option value="Christian">Christian</option>
        <option value="Sikh">Sikh</option>
        <option value="Other">Other</option>
    </select>

    <label for="state">State:</label>
    <select id="state" name="state" required>
        <option value="">Select state</option>
        <option value="Delhi">Delhi</option>
        <option value="Maharashtra">Maharashtra</option>
        <option value="Punjab">Punjab</option>
        <option value="Tamil Nadu">Tamil Nadu</option>
        <option value="Other">Other</option>
    </select>

    <label for="caste">Caste:</label>
    <select id="caste" name="caste" required>
        <option value="">Select caste</option>
        <option value="Brahmin">Brahmin</option>
        <option value="Rajput">Rajput</option>
        <option value="Kayastha">Kayastha</option>
        <option value="Jat">Jat</option>
        <option value="Other">Other</option>
    </select>

    <label for="education">Education:</label>
    <select id="education" name="education" required>
        <option value="">Select education</option>
        <option value="High School">High School</option>
        <option value="Graduate">Graduate</option>
        <option value="Post Graduate">Post Graduate</option>
        <option value="PhD">PhD</option>
        <option value="Other">Other</option>
    </select>

    <label for="city1">City:</label>
    <select id="city1" name="city1" required>
        <option value="">Select city</option>
        <option value="Delhi">Delhi</option>
        <option value="Mumbai">Mumbai</option>
        <option value="Pune">Pune</option>
        <option value="Other">Other</option>
    </select>

    <input type="submit" value="Register">

</form>

</body>
</html>
2. Register.java
package in.sp.backend;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/regForm")
public class Register extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req,
                           HttpServletResponse resp)
            throws ServletException, IOException {

        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();

        String myname = req.getParameter("name1");
        String myemail = req.getParameter("email1");
        String mypass = req.getParameter("pass1");
        String mygender = req.getParameter("gender1");
        String city = req.getParameter("city1");

        try {

            Class.forName("com.mysql.cj.jdbc.Driver");

            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/Sql_Db",
                "root",
                "root"
            );

            String sql = "INSERT INTO register VALUES (?, ?, ?, ?, ?)";

            PreparedStatement ps = con.prepareStatement(sql);

            ps.setString(1, myname);
            ps.setString(2, myemail);
            ps.setString(3, mypass);
            ps.setString(4, mygender);
            ps.setString(5, city);

            int count = ps.executeUpdate();

            if (count > 0) {

                out.println(
                    "<h3 style='color:green'>"
                    + "User registered Successfully!"
                    + "</h3>"
                );

                RequestDispatcher rd =
                    req.getRequestDispatcher("/register.jsp");

                rd.include(req, resp);

            } else {

                out.println(
                    "<h3 style='color:red'>"
                    + "User not registered due to some error"
                    + "</h3>"
                );

                RequestDispatcher rd =
                    req.getRequestDispatcher("/register.jsp");

                rd.include(req, resp);
            }

            ps.close();
            con.close();

        } catch (Exception e) {

            e.printStackTrace();

            out.println(
                "<h3 style='color:red'>"
                + "Exception Occurred: " + e.getMessage()
                + "</h3>"
            );

            RequestDispatcher rd =
                req.getRequestDispatcher("/register.jsp");

            rd.include(req, resp);
        }
    }
}
3. database.sql

GitHub par database ka SQL code bhi upload karna useful hai:

CREATE DATABASE Sql_Db;

USE Sql_Db;

CREATE TABLE register (
    name VARCHAR(100),
    email VARCHAR(100),
    password VARCHAR(100),
    gender VARCHAR(20),
    city VARCHAR(100)
);

