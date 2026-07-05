SELECT * FROM books;
SELECT * FROM branch;
SELECT * FROM employees;
SELECT * FROM issued_status;
SELECT * FROM return_status;
SELECT * FROM members;
 
ALTER TABLE return_status
ADD book_quality VARCHAR(15) DEFAULT 'Good';
 
UPDATE return_status
SET book_quality = 'Damaged'
WHERE issued_id IN ('IS112', 'IS117', 'IS118');
 
SELECT * FROM return_status;
SELECT * FROM books;
SELECT * FROM branch;
SELECT * FROM employees;
SELECT * FROM issued_status;
SELECT * FROM members;
 
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 
 'Harper Lee', 'J.B. Lippincott & Co.');
 
SELECT * FROM books;
 
UPDATE members
SET member_address = '125 Main St'
WHERE member_id = 'C101';
 
SELECT * FROM members;
 
SELECT * FROM issued_status
WHERE issued_id = 'IS121';
 
DELETE FROM issued_status
WHERE issued_id = 'IS121';
 
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101';
 
SELECT 
    ist.issued_emp_id,
    e.emp_name
FROM issued_status AS ist
JOIN employees AS e
    ON e.emp_id = ist.issued_emp_id
GROUP BY 1, 2
HAVING COUNT(ist.issued_id) > 1;
 
DROP TABLE IF EXISTS book_cnts;
 
CREATE TABLE book_cnts AS    
SELECT 
    b.isbn,
    b.book_title,
    COUNT(ist.issued_id) AS no_issued
FROM books AS b
JOIN issued_status AS ist
    ON ist.issued_book_isbn = b.isbn
GROUP BY 1, 2;
 
SELECT * FROM book_cnts;
 
SELECT * FROM books
WHERE category = 'Classic';
 
SELECT
    b.category,
    SUM(b.rental_price) AS total_revenue,
    COUNT(*) AS total_issued
FROM books AS b
JOIN issued_status AS ist
    ON ist.issued_book_isbn = b.isbn
GROUP BY 1;
 
SELECT * FROM members
WHERE reg_date >= CURDATE() - INTERVAL 180 DAY;
 
INSERT IGNORE INTO members(member_id, member_name, member_address, reg_date)
VALUES
('C118', 'Sam', '145 Main St', '2024-06-01'),
('C119', 'John', '133 Main St', '2024-05-01');
 
SELECT 
    e1.*,
    b.manager_id,
    e2.emp_name AS manager
FROM employees AS e1
JOIN branch AS b
    ON b.branch_id = e1.branch_id
JOIN employees AS e2
    ON b.manager_id = e2.emp_id;
 
DROP TABLE IF EXISTS books_price_greater_than_seven;
 
CREATE TABLE books_price_greater_than_seven AS    
SELECT * FROM books
WHERE rental_price > 7;
 
SELECT * FROM books_price_greater_than_seven;
 
SELECT DISTINCT ist.issued_book_name
FROM issued_status AS ist
LEFT JOIN return_status AS rs
    ON ist.issued_id = rs.issued_id
WHERE rs.return_id IS NULL;
 
SELECT * FROM return_status;
SELECT * FROM books;
SELECT * FROM branch;
SELECT * FROM employees;
SELECT * FROM issued_status;
SELECT * FROM members;
 
SELECT CURDATE();
 
SELECT 
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    DATEDIFF(CURDATE(), ist.issued_date) AS over_due_days
FROM issued_status AS ist
JOIN members AS m
    ON m.member_id = ist.issued_member_id
JOIN books AS bk
    ON bk.isbn = ist.issued_book_isbn
LEFT JOIN return_status AS rs
    ON rs.issued_id = ist.issued_id
WHERE 
    rs.return_date IS NULL
    AND DATEDIFF(CURDATE(), ist.issued_date) > 30
ORDER BY 1;
 
SELECT * FROM issued_status
WHERE issued_book_isbn = '978-0-330-25864-8';
 
SELECT * FROM books
WHERE isbn = '978-0-451-52994-2';
 
UPDATE books
SET status = 'no'
WHERE isbn = '978-0-451-52994-2';
 
SELECT * FROM return_status
WHERE issued_id = 'IS130';
 
INSERT INTO return_status(return_id, issued_id, return_date, book_quality)
VALUES
('RS125', 'IS130', CURDATE(), 'Good');
 
SELECT * FROM return_status
WHERE issued_id = 'IS130';
 
ALTER TABLE return_status
ADD COLUMN IF NOT EXISTS book_quality VARCHAR(15) DEFAULT 'Good';
 
DROP PROCEDURE IF EXISTS add_return_records;
 
DELIMITER $$
 
CREATE PROCEDURE add_return_records(
    p_return_id VARCHAR(10), 
    p_issued_id VARCHAR(10), 
    p_book_quality VARCHAR(10)
)
BEGIN
    DECLARE v_isbn VARCHAR(50);
    DECLARE v_book_name VARCHAR(80);
    
    INSERT INTO return_status(return_id, issued_id, return_date, book_quality)
    VALUES (p_return_id, p_issued_id, CURDATE(), p_book_quality);
 
    SELECT 
        issued_book_isbn,
        issued_book_name
    INTO v_isbn, v_book_name
    FROM issued_status
    WHERE issued_id = p_issued_id;
 
    UPDATE books
    SET status = 'yes'
    WHERE isbn = v_isbn;
 
    SELECT CONCAT('Thank you for returning the book: ', v_book_name) AS message;
    
END$$
 
DELIMITER ;
 
SELECT * FROM books
WHERE isbn = '978-0-307-58837-1';
 
SELECT * FROM issued_status
WHERE issued_book_isbn = '978-0-307-58837-1';
 
SELECT * FROM return_status
WHERE issued_id = 'IS135';
 
CALL add_return_records('RS138', 'IS135', 'Good');
CALL add_return_records('RS148', 'IS140', 'Good');
 
SELECT * FROM branch;
SELECT * FROM issued_status;
SELECT * FROM employees;
SELECT * FROM books;
SELECT * FROM return_status;
 
DROP TABLE IF EXISTS branch_reports;
 
CREATE TABLE branch_reports AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id) AS number_book_issued,
    COUNT(rs.return_id) AS number_of_book_return,
    SUM(bk.rental_price) AS total_revenue
FROM issued_status AS ist
JOIN employees AS e
    ON e.emp_id = ist.issued_emp_id
JOIN branch AS b
    ON e.branch_id = b.branch_id
LEFT JOIN return_status AS rs
    ON rs.issued_id = ist.issued_id
JOIN books AS bk
    ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;
 
SELECT * FROM branch_reports;
 
DROP TABLE IF EXISTS active_members;
 
CREATE TABLE active_members AS
SELECT * FROM members
WHERE member_id IN (
    SELECT DISTINCT issued_member_id   
    FROM issued_status
    WHERE issued_date >= CURDATE() - INTERVAL 2 MONTH
);
 
SELECT * FROM active_members;
 
SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) AS no_book_issued
FROM issued_status AS ist
JOIN employees AS e
    ON e.emp_id = ist.issued_emp_id
JOIN branch AS b
    ON e.branch_id = b.branch_id
GROUP BY 1, 2;
 
DROP PROCEDURE IF EXISTS issue_book;
 
DELIMITER $$
 
CREATE PROCEDURE issue_book(
    p_issued_id VARCHAR(10), 
    p_issued_member_id VARCHAR(30), 
    p_issued_book_isbn VARCHAR(30), 
    p_issued_emp_id VARCHAR(10)
)
BEGIN
    DECLARE v_status VARCHAR(10);
 
    SELECT status 
    INTO v_status
    FROM books
    WHERE isbn = p_issued_book_isbn;
 
    IF v_status = 'yes' THEN
        INSERT INTO issued_status(
            issued_id, 
            issued_member_id, 
            issued_date, 
            issued_book_isbn, 
            issued_emp_id
        )
        VALUES(
            p_issued_id, 
            p_issued_member_id, 
            CURDATE(), 
            p_issued_book_isbn, 
            p_issued_emp_id
        );
 
        UPDATE books
        SET status = 'no'
        WHERE isbn = p_issued_book_isbn;
 
        SELECT CONCAT('Book issued successfully for ISBN: ', 
                       p_issued_book_isbn) AS message;
    ELSE
        SELECT CONCAT('Sorry, book is unavailable. ISBN: ', 
                       p_issued_book_isbn) AS message;
    END IF;
    
END$$
 
DELIMITER ;
 
SELECT * FROM books;
SELECT * FROM issued_status;
 
CALL issue_book('IS155', 'C108', '978-0-553-29698-2', 'E104');
 
CALL issue_book('IS156', 'C108', '978-0-375-41398-8', 'E104');
 
SELECT * FROM books
WHERE isbn = '978-0-375-41398-8';
 
