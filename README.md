CREATE SCHEMA postassessment;
USE postassessment;
-- 1
CREATE TABLE transactions (
    transaction_id INT PRIMARY KEY,
    user_id INT,
    payment_method VARCHAR(255),
    amount DECIMAL(10,2),
    transaction_date DATE,
    status VARCHAR(255)
);
INSERT INTO transactions (transaction_id, user_id, payment_method, amount, transaction_date, status)
VALUES
(101, 202, 'Credit Card', 200.43, '2025-02-16', 'Completed'),
(102, 203, 'Netbanking', 3233.10, '2025-03-11', 'Failed'),
(103, 203, 'Netbanking', 1195.35, '2025-02-24', 'Failed'),
(104, 203, 'Debit Card', 376.11, '2025-03-10', 'Failed'),
(105, 203, 'Netbanking', 112.01, '2025-04-04', 'Failed'),
(106, 203, 'Credit Card', 111.10, '2025-09-12', 'Failed'),
(107, 203, 'Debit Card', 2344.50, '2025-10-03', 'Failed');

SELECT user_id,COUNT(status) AS failed_transactions,
COUNT(DISTINCT(payment_method))AS distinct_payment_methods
FROM transactions  GROUP BY user_id HAVING COUNT(status)>5;

-- 2
CREATE TABLE support_tickets (
    id INT PRIMARY KEY,
    customer_id INT,
    created_at VARCHAR(19),
    resolved_at VARCHAR(19)
);

INSERT INTO support_tickets (id, customer_id, created_at, resolved_at) VALUES
(1, 1, '2023-12-21 05:42:00', '2024-01-01 05:42:00'),
(2, 2, '2023-07-08 14:22:00', NULL),
(3, 3, '2023-05-22 08:54:00', '2023-06-17 08:54:00');
SELECT 
ROUND(AVG(TIMESTAMPDIFF(SECOND,
STR_TO_DATE(created_at,'%Y-%m-%d %H:%i:%s')
,STR_TO_DATE(resolved_at,'%Y-%m-%d %H:%i:%s'))/3600),2)
AS average_response_time FROM support_tickets
 WHERE resolved_at IS NOT NULL; -- // the null value row will skip 
 

-- 3
CREATE TABLE customers3 (
    id INT PRIMARY KEY,
    name VARCHAR(255),
    city VARCHAR(255)
);

CREATE TABLE orders3 (
    id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers3(id)
);
-- Insert into customers
INSERT INTO customers3 (id, name, city) VALUES
(1, 'Customer 1', 'Los Angeles'),
(2, 'Customer 2', 'Chicago'),
(3, 'Customer 3', 'Chicago');

-- Insert into orders
INSERT INTO orders3 (id, customer_id, amount) VALUES
(1, 1, 150.75),
(2, 2, 230.50),
(3, 3, 345.25);

SELECT 
    c.id AS customer_id,
    c.name,
    c.city,
    FLOOR(SUM(o.amount)) AS total_spending
FROM customers3 c
JOIN orders3 o ON c.id = o.customer_id
GROUP BY c.id, c.name, c.city
HAVING FLOOR(SUM(o.amount)) = (
    SELECT MAX(city_total)
    FROM (
        SELECT FLOOR(SUM(o2.amount)) AS city_total
        FROM customers3 c2
        JOIN orders3 o2 ON c2.id = o2.customer_id
        WHERE c2.city = c.city
        GROUP BY c2.id
    ) AS city_totals
);

-- 4
CREATE TABLE products4 (
    id INT PRIMARY KEY,
    name VARCHAR(255),
    category VARCHAR(255),
    is_available SMALLINT
);

CREATE TABLE requests4 (
    product_id INT,
    client_email VARCHAR(255),
    FOREIGN KEY (product_id) REFERENCES products4(id)
);
INSERT INTO products4 (id, name, category, is_available) VALUES
(1, 'PromoPro', 'beauty products', 1),
(2, 'AdVantage', 'outdoor gear', 1),
(3, 'MarketMagnet', 'sports equipment', 1),
(5, 'AdBlitz', 'beauty products', 0);

INSERT INTO requests4 (product_id, client_email) VALUES
(1, 'salgate1@fc2.com'),
(1, 'lwycliff6@list-manage.com'),
(1, 'ekimbleyf@scientificamerican.com'),
(2, 'bgooro@spotify.com'),
(2, 'vsamwayest@bbb.org'),
(3, 'apappin0@yellowbook.com'),
(3, 'ringreyb@businessinsider.com'),
(3, 'mrysonm@istockphoto.com'),
(5, 'ayushin1c@opera.com'),
(5, 'bcoulston1q@hubpages.com');

SELECT p.name AS product_name, COUNT(*) AS total_requests 
FROM products4 p JOIN requests4 r ON p.id=r.product_id
WHERE is_available=1 GROUP BY p.name 
ORDER BY total_requests DESC,product_name ASC;
--------------------------------
-- 5
CREATE TABLE campaigns5 (
    id INT PRIMARY KEY,
    name VARCHAR(255),
    is_active SMALLINT
);

CREATE TABLE engagements5 (
    campaign_id INT,
    views INT,
    clicks INT,
    FOREIGN KEY (campaign_id) REFERENCES campaigns5(id)
);
INSERT INTO campaigns5 (id, name, is_active) VALUES
(1, 'SummerSavings', 1),
(2, 'FallFrenzy', 1),
(3, 'WinterWonderland', 0);

INSERT INTO engagements5 (campaign_id, views, clicks) VALUES
(1, 100, 10),
(1, 150, 20),
(2, 200, 30),
(2, 250, 40),
(3, 300, 50),
(1, 120, 15),
(2, 180, 25),
(3, 220, 35),
(1, 130, 18),
(2, 210, 28);

SELECT c.name AS campaign_name,COUNT(*) AS total_engagements,
SUM(e.views)+SUM(e.clicks) AS total_views_and_clicks 
FROM campaigns5 c JOIN engagements5 e ON c.id=e.campaign_id
WHERE c.is_active=1
GROUP BY c.id,c.name ORDER BY campaign_name ASC; -- // because in one campaign can have duplicate name


-- 6
CREATE TABLE accounts (
    id INT PRIMARY KEY,
    email VARCHAR(255)
);
DROP TABLE IF EXISTS accounts6;
CREATE TABLE reports (
    account_id INT,
    dt VARCHAR(19),
    amount DECIMAL(6,2),
    FOREIGN KEY (account_id) REFERENCES accounts(id)
);
INSERT INTO accounts (id, email) VALUES
(1, 'hratke0@disqus.com'),
(2, 'lcaiger1@si.edu'),
(3, 'gburkett2@vinaora.com');

INSERT INTO reports (account_id, dt, amount) VALUES
(1, '2023-05-27 01:46:19', 830.45),
(2, '2023-01-15 09:23:21', 2518.18),
(3, '2023-05-08 01:44:41', 4637.39),
(1, '2023-06-30 15:02:03', 3953.69),
(2, '2023-12-05 04:39:31', 3357.99),
(3, '2023-02-03 09:41:00', 1907.38),
(1, '2022-12-30 04:05:57', 1217.29),
(2, '2024-01-24 14:18:07', 2441.66),
(3, '2024-01-05 23:19:31', 3055.20),
(1, '2023-05-26 01:54:24', 2077.36);

SELECT a.email AS email,ROUND(SUM(r.amount),2) AS total_report_amoount
FROM accounts a JOIN reports r ON a.id=r.account_id
WHERE r.dt LIKE '2023%' GROUP BY a.email ORDER BY email ASC;

-- 7
CREATE TABLE devices (
    id INT NOT NULL PRIMARY KEY,
    mac_address VARCHAR(255)
);

INSERT INTO devices (id, mac_address) VALUES
(1, '66-0F-84-41-8B-8E'),
(2, 'A6-1A-2F-3A-7B-83'),
(3, '76-CD-24-4B-F0-DD');

CREATE TABLE scanned_files (
    device_id INT,
    filename VARCHAR(255),
    is_infected SMALLINT,
    FOREIGN KEY (device_id) REFERENCES devices(id)
);

INSERT INTO scanned_files (device_id, filename, is_infected) VALUES
(1, 'File1.mp3', 0),
(1, 'File2.xls', 1),
(2, 'File3.doc', 0),
(2, 'File4.ppt', 1),
(2, 'File5.mp3', 1),
(3, 'File6.xls', 0),
(3, 'File7.doc', 1),
(3, 'File8.ppt', 0),
(3, 'File9.mp3', 1),
(3, 'File10.xls', 0);
SELECT d.mac_address AS mac_address,
COUNT(*) AS total_filed_scanned,
SUM(CASE
	WHEN s.is_infected=1 THEN 1 ELSE 0
    END)
AS total_infected_files FROM devices d
 JOIN scanned_files s
ON d.id=s.device_id 
GROUP BY d.mac_address ORDER BY mac_address ASC;


-- 8
CREATE TABLE coins (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE transactions (
    coin_id INT,
    dt VARCHAR(19),
    amount DECIMAL(5,2),
    FOREIGN KEY (coin_id) REFERENCES coins(id)
);
-- Insert into coins
INSERT INTO coins (id, name) VALUES
(1, 'BitCash'),
(2, 'Etherium'),
(3, 'Litecoin');

-- Insert into transactions
INSERT INTO transactions (coin_id, dt, amount) VALUES
(1, '2023-07-03 12:16:53', 34.32),
(1, '2023-12-08 12:14:58', 47.59),
(2, '2022-12-16 20:42:10', 45.54),
(2, '2023-11-05 09:27:11', 53.30),
(3, '2023-12-05 06:45:23', 71.51),
(3, '2023-01-19 01:43:25', 97.18),
(3, '2024-01-24 13:34:00', 86.68),
(1, '2023-05-07 05:30:06', 25.60),
(2, '2023-03-08 08:07:20', 40.11),
(3, '2023-08-13 10:44:54', 87.54);

SELECT c.name AS coin_name,ROUND(SUM(t.amount),2)AS total_transaction_amount,
COUNT(*) AS total_transactions FROM coins c JOIN transactions t
ON c.id=t.coin_id WHERE t.dt LIKE '2023%'
GROUP BY c.name ORDER BY c.name ASC;

-- 9
CREATE TABLE customers (
    id INT NOT NULL PRIMARY KEY,
    email VARCHAR(255)
);

CREATE TABLE domains (
    customer_id INT,
    name VARCHAR(255),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

INSERT INTO customers (id, email) VALUES
    (1, 'ebaydon0@washingtonpost.com'),
    (2, 'agammade1@comcast.net'),
    (3, 'goloshkin2@reference.com'),
    (4, 'cantonescu3@earthlink.net'),
    (5, 'fparzis4@ow.ly'),
    (6, 'cpetroulis5@shutterfly.com'),
    (7, 'tbeels6@bbb.org'),
    (8, 'zmacturlough7@4shared.com'),
    (9, 'eshury8@skype.com'),
    (10, 'jfehners9@github.io');
    INSERT INTO domains (customer_id, name) VALUES
    (1, 'bfiilipa.net'),
    (1, 'gsparsholti.net'),
    (1, 'jhughsr.org'),
    (2, 'scopas8.net'),
    (2, 'cglison1u.org'),
    (3, 'tginiz.com'),
    (3, 'arubinowitsch2l.net'),
    (3, 'clockyear2m.org'),
    (4, 'sfinnigand.com'),
    (4, 'vborrelt.net');
SELECT c.email AS email,COUNT(*) AS total_domains FROM customers c JOIN domains d
ON c.id=d.customer_id GROUP BY c.email ORDER BY  email ASC;
-- 10
CREATE TABLE products (
    id INT NOT NULL PRIMARY KEY,
    name VARCHAR(255),
    price DECIMAL(6, 2),
    in_stock SMALLINT
);

CREATE TABLE wishlists (
    product_id INT,
    customer_email VARCHAR(255),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
INSERT INTO products (id, name, price, in_stock) VALUES
    (1, 'TechGadget Pro X', 324.24, 1),
    (2, 'LuxuryHome Decor Set', 884.90, 1),
    (3, 'FitnessTracker Elite', 698.59, 0);
    
    INSERT INTO wishlists (product_id, customer_email) VALUES
    (1, 'user1@example.com'),
    (1, 'user2@example.com'),
    (2, 'user3@example.com'),
    (2, 'user4@example.com'),
    (2, 'user5@example.com'),
    (3, 'user6@example.com'),
    (1, 'user7@example.com'),
    (2, 'user8@example.com'),
    (1, 'user9@example.com'),
    (3, 'user10@example.com');
SELECT p.name AS product_name,ROUND(SUM(p.price),2) AS price,
COUNT(*) AS total_wishlist_count
FROM products p JOIN  wishlists w ON p.id=w.product_id
WHERE p.in_stock=1 GROUP BY p.name ORDER BY product_name ASC;

-- 11
CREATE TABLE campaigns (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE email_stats (
    campaign_id INT,
    emails_sent INT,
    emails_opened INT,
    FOREIGN KEY (campaign_id) REFERENCES campaigns(id)
);
-- Campaigns
INSERT INTO campaigns (id, name) VALUES
(1, 'SummerSale2021'),
(2, 'FallPromo'),
(3, 'WinterWonderland');

-- Email stats
INSERT INTO email_stats (campaign_id, emails_sent, emails_opened) VALUES
(1, 1000, 800),
(2, 1500, 1200),
(3, 2000, 1800),
(1, 500, 300),
(2, 700, 500),
(3, 800, 600),
(1, 300, 200),
(2, 400, 300),
(3, 600, 500),
(3, 400, 300);

SELECT c.name AS campaign_name,SUM(e.emails_sent) 
AS total_emails_sent,SUM(e.emails_opened) AS total_emails_opened,
SUM(e.emails_sent)-SUM(e.emails_opened)AS total_email_not_opened
FROM campaigns c JOIN email_stats e ON c.id=e.campaign_id
GROUP BY campaign_name ORDER BY campaign_name ASC;

-- 12
CREATE TABLE lots (
    id INT NOT NULL PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE offers (
    lot_id INT,
    amount DECIMAL(6, 2),
    FOREIGN KEY (lot_id) REFERENCES lots(id)
);

INSERT INTO lots (id, name) VALUES
    (1, 'Acacia parramattensis Tindale'),
    (2, 'Poa arctica R. Br. ssp. aperta (Scribn. & Merr.) Soreng'),
    (3, 'Calophyllum inophyllum L.');
    INSERT INTO offers (lot_id, amount) VALUES
    (1, 260.91),
    (1, 802.83),
    (1, 986.78),
    (2, 814.57),
    (2, 999.06),
    (2, 414.67),
    (3, 200.41),
    (3, 593.07),
    (3, 701.88),
    (3, 972.87);
SELECT l.name AS lot_name,MAX(o.amount) AS highest_offer,COUNT(*) AS total_offers
FROM lots l JOIN offers o ON l.id=o.lot_id GROUP BY lot_name
ORDER BY lot_name ASC;

-- 13
 CREATE TABLE accounts13 (
    id INT NOT NULL PRIMARY KEY,
    iban VARCHAR(255)
);

CREATE TABLE transactions13 (
    account_id INT,
    dt CHAR(19),
    amount DECIMAL(5, 2),
    FOREIGN KEY (account_id) REFERENCES accounts13(id)
);
DROP TABLE IF EXISTS transactions13;
INSERT INTO accounts13 (id, iban) VALUES
    (1, 'BG40 RFFX 4896 53DD CZD6 KQ'),
    (2, 'PT42 5267 0592 8451 8001 2180 3'),
    (3, 'FR96 8758 8909 81LR DJ71 ERKN D56');
    INSERT INTO transactions13 (account_id, dt, amount) VALUES
    (1, '2022-09-02 06:33:39', 33.31),
    (1, '2022-09-20 08:14:39', 31.77),
    (1, '2022-09-25 06:41:45', 72.84),
    (2, '2022-09-04 22:28:12', 35.26),
    (2, '2022-09-17 07:57:29', 33.27),
    (2, '2022-09-27 22:30:36', 70.78),
    (3, '2022-09-16 21:54:12', 75.04),
    (3, '2022-09-19 18:27:39', 71.19),
    (3, '2022-09-28 01:38:56', 14.34),
    (3, '2022-08-30 01:35:31', 69.19);
SELECT a.iban AS iban,MIN(t.amount)AS min_transaction,MAX(t.amount)
AS max_transaction,
AVG(t.amount) AS avg_transaction,COUNT(*)AS total_transactions
 FROM accounts13 a
JOIN transactions13 t ON a.id=t.account_id WHERE t.dt LIKE '2022-09%'
GROUP BY a.iban ORDER BY iban;

-- 14
CREATE TABLE products14(
    id INT NOT NULL PRIMARY KEY,
    name VARCHAR(255),
    price DECIMAL(6, 2),
    in_stock SMALLINT
);

CREATE TABLE wishlists14 (
    product_id INT,
    customer_email VARCHAR(255),
    FOREIGN KEY (product_id) REFERENCES products14(id)
);

INSERT INTO products14 (id, name, price, in_stock) VALUES
    (1, 'TechGadget Pro X', 274.80, 1),
    (2, 'LuxuryHome Decor Set', 262.84, 1),
    (3, 'FitnessTracker Elite', 637.92, 0),
    (4, 'GourmetCookware Set', 535.34, 1),
    (5, 'Fashionista Wardrobe Collection', 525.44, 1);
    INSERT INTO wishlists14 (product_id, customer_email) VALUES
    (1, 'crabbage@redcross.org'),
    (1, 'efindlow2@tinypic.com'),
    (1, 'jmachoste5@issuu.com'),
    (1, 'nselle@simplemachines.org'),
    (2, 'aonn1@ebay.co.uk'),
    (2, 'bbolton0@google.cn'),
    (2, 'ebockett3@storify.com'),
    (2, 'fdunguyg@symantec.com'),
    (2, 'slowried@cbsnews.com'),
    (3, 'jgately7@goo.ne.jp'),
    (3, 'ospearetti@bandcamp.com'),
    (3, 'rpahonsb@paypal.com'),
    (3, 'ydevauxh@toplist.cz'),
    (3, 'zbabbage9@imageshack.us'),
    (4, 'dpauleyac@cnbc.com'),
    (4, 'jletterick4@dailymotion.com'),
    (4, 'jrimmsett6@princeton.edu'),
    (4, 'skernell@uiuc.edu'),
    (5, 'blodina@wikimedia.org'),
    (5, 'lyusupovi@nps.gov');
    
SELECT 
    p.name AS name,
    p.price AS price,
    COUNT(*) AS total_wishes
FROM products14 p
JOIN wishlists14 w 
    ON p.id = w.product_id
WHERE p.in_stock = 1
GROUP BY p.name, p.price
ORDER BY total_wishes DESC, p.name ASC
LIMIT 3;

-- 15
CREATE TABLE customers15 (
    id INT NOT NULL PRIMARY KEY,
    email VARCHAR(255)
);

CREATE TABLE purchases15 (
    customer_id INT,
    dt VARCHAR(19),
    amount DECIMAL(6, 2),
    FOREIGN KEY (customer_id) REFERENCES customers15(id)
);
DROP TABLE IF EXISTS purchases15;
INSERT INTO customers15 (id, email) VALUES
    (1, 'floggie0@newsvine.com'),
    (2, 'sgillbe1@ca.gov'),
    (3, 'jgohn2@elegantthemes.com');
    INSERT INTO purchases15 (customer_id, dt, amount) VALUES
    (2, '2024-02-21 02:56:12', 228.58),
    (2, '2024-02-23 09:52:47', 972.41),
    (1, '2024-03-14 15:50:13', 109.16),
    (1, '2024-03-17 00:31:44', 11.49),
    (1, '2024-03-17 06:15:42', 692.64),
    (2, '2024-03-01 06:35:09', 589.74),
    (2, '2024-03-14 14:12:23', 508.75),
    (2, '2024-03-17 07:51:38', 933.93),
    (2, '2024-03-19 08:24:38', 488.26),
    (2, '2024-03-21 23:33:54', 55.07),
    (3, '2024-03-01 17:34:30', 816.67),
    (3, '2024-03-08 23:46:02', 672.93),
    (3, '2024-03-15 18:09:56', 260.66),
    (3, '2024-03-20 15:18:11', 321.07),
    (3, '2024-03-20 17:46:35', 29.06),
    (3, '2024-03-23 20:23:49', 314.85),
    (3, '2024-03-25 11:41:07', 67.12),
    (1, '2024-04-05 03:05:10', 417.78),
    (2, '2024-04-09 08:16:17', 697.53),
    (3, '2024-04-02 07:56:48', 156.27);
SELECT c.email AS email,COUNT(*)AS total_purchases,
SUM(p.amount)AS total_purchases_amount
FROM customers15 c JOIN purchases15 p ON c.id=p.customer_id
WHERE p.dt LIKE '2024-03%' GROUP BY c.id,c.email 
ORDER BY email ASC;

-- 16
 CREATE TABLE applicants16 (
    id INT NOT NULL PRIMARY KEY,
    email VARCHAR(255)
);

CREATE TABLE appointments16 (
    applicant_id INT,
    dt VARCHAR(19),
    is_received BOOLEAN,
    FOREIGN KEY (applicant_id) REFERENCES applicants16(id)
);

INSERT INTO applicants16 (id, email) VALUES
    (1, 'nkienzle0@spiegel.de'),
    (2, 'alaste1@bbc.co.uk'),
    (3, 'jjochanany2@ow.ly'),
    (4, 'bsenn3@salon.com'),
    (5, 'bwhittall4@nhs.uk');

INSERT INTO appointments16 (applicant_id, dt, is_received) VALUES
    (1, '2024-04-27', FALSE),
    (2, '2024-04-01', FALSE),
    (3, '2024-04-15', FALSE),
    (4, '2024-03-27', FALSE),
    (5, '2024-03-26', TRUE);
SELECT a.email AS email,STR_TO_DATE(b.dt,'%Y-%m-%d')AS scheduled_appointment ,
DATEDIFF('2024-04-10',STR_TO_DATE(b.dt,'%Y-%m-%d'))AS days_of_delay 
FROM applicants16 a JOIN appointments16 b ON a.id=b.applicant_id
WHERE b.is_received IS FALSE AND b.dt<'2024-04-10'
  ORDER BY scheduled_appointment ASC,
email ASC;
-- 17
CREATE TABLE applicants17 (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE appointments17 (
  applicant_id INT,
  dt VARCHAR(19),
  FOREIGN KEY (applicant_id) REFERENCES applicants17(id)
);

INSERT INTO applicants17 (id, email) VALUES
(1, 'rastlatto0@instagram.com'),
(2, 'gcarmody1@stanford.edu'),
(3, '_mgreenset2@state.tx.us');

INSERT INTO appointments17 (applicant_id, dt) VALUES
(1, '2024-05-26 01:36:43'),
(2, '2024-05-27 16:30:28'),
(3, '2024-05-18 19:28:52');

SELECT 
    a.email AS email, 
    DAYNAME(STR_TO_DATE(b.dt, '%Y-%m-%d %H:%i:%s')) AS scheduled_appointment
FROM applicants17 a 
JOIN appointments17 b ON a.id = b.applicant_id
WHERE DAYOFWEEK(STR_TO_DATE(b.dt, '%Y-%m-%d %H:%i:%s')) IN (1,7)
ORDER BY a.email ASC;

-- 18
CREATE TABLE countries18 (
  id INT PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE domains18 (
  country_id INT,
  name VARCHAR(255),
  is_active BOOLEAN,
  FOREIGN KEY (country_id) REFERENCES countries18(id)
);

INSERT INTO countries18 (id, name) VALUES
(1, 'Azerbaijan'),
(2, 'Colombia'),
(3, 'China');
INSERT INTO domains18 (country_id, name, is_active) VALUES
(1, 'angelfire.com', 1),
(1, 'free.fr', 1),
(1, 'google.cn', 1),
(1, 'nationalgeographic.com', 1),
(1, 'ovh.net', 1),
(1, 'surveymonkey.com', 1),
(1, 'twitpic.com', 1),
(2, 'ameblo.jp', 1),
(2, 'berkeley.edu', 1),
(2, 'mulishly.com', 1),
(2, 'redcross.org', 1),
(2, 'sourceforg.e.net', 1),
(3, 'tv360.com', 1),
(3, 'tiventeme.ru', 1),
(3, 'squidoo.com', 1),
(3, 'technorati.com', 1),
(3, 'webnode.com', 1),
(3, 'yahoo.co.jp', 1),
(1, 'tuni.dt.de', 0),
(1, 'qq.com', 0);

SELECT c.name AS country_name,COUNT(*) AS total_domains 
FROM countries18 c
JOIN domains18 d ON c.id=d.country_id WHERE d.is_active IS TRUE
GROUP BY c.name ORDER BY country_name ASC;

-- 19
CREATE TABLE domains19 (
  name VARCHAR(255),
  next_renewal_date VARCHAR(19)
);

INSERT INTO domains19 (name, next_renewal_date) VALUES
('wired.com', '2024-06-14 00:10:12'),
('blogger.com', '2024-07-18 05:54:57'),
('com.com', '2024-07-21 02:57:25');
SELECT name,
	DATE ('2024-04-10') AS today_date, -- it also can transfer in date
    STR_TO_DATE(next_renewal_date,'%Y-%m-%d %H:%i:%s')
    AS next_renewal_date,-- it also can transfer into date
   DATEDIFF(STR_TO_DATE(next_renewal_date,'%Y-%m-%d %H:%i:%s'),
   '2024-04-10')AS days_util_renewal
    FROM domains19 ORDER BY days_util_renewal ASC,name ASC;
-- 20
CREATE TABLE users20 (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE transactions20 (
  user_id INT,
  dt VARCHAR(19),
  amount DECIMAL(5, 2),
  FOREIGN KEY (user_id) REFERENCES users20(id)
);

INSERT INTO users20 (id, email) VALUES
(1, 'bblaszczynski0@devhub.com'),
(2, 'dwoodey1@chronoengine.com'),
(3, 'flerway2@wikpedia.org');
INSERT INTO transactions20 (user_id, dt, amount) VALUES
(1, '2024-02-23 19:30:03', 942.50),
(1, '2024-03-07 09:01:15', 855.22),
(1, '2024-04-01 04:18:41', 253.35),
(1, '2024-04-07 02:40:58', 886.88),
(2, '2024-02-25 05:11:39', 957.77),
(2, '2024-03-06 03:00:40', 413.39),
(2, '2024-03-07 14:41:03', 906.16),
(2, '2024-03-10 00:58:13', 116.59),
(2, '2024-09-13 23:18:29', 550.31),
(2, '2024-01-22 03:07:49', 196.23),
(2, '2024-03-24 03:25:14', 899.76),
(2, '2024-03-25 12:28:18', 398.07),
(2, '2024-03-27 09:11:15', 212.31),
(2, '2024-04-09 00:33:26', 97.85),
(3, '2024-03-01 17:24:46', 323.11),
(3, '2024-03-03 10:16:06', 673.23),
(3, '2024-03-08 16:19:46', 236.74),
(3, '2024-03-23 15:37:47', 234.87),
(3, '2024-04-05 20:35:45', 989.35),
(3, '2024-04-07 05:26:35', 369.20);
SELECT u.email AS email,COUNT(*)AS total_trnsactions,MIN(t.amount) AS min_amount,MAX(t.amount)AS max_amount,
SUM( t.amount) AS total_amount
FROM users20 u JOIN transactions20 t ON u.id=t.user_id
WHERE t.dt LIKE '2024-03%' GROUP BY u.email ORDER BY email ASC;

-- 21
CREATE TABLE users21 (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE transactions21 (
  user_id INT,
  dt VARCHAR(19),
  amount DECIMAL(5, 2),
  FOREIGN KEY (user_id) REFERENCES users21(id)
);

INSERT INTO users21 (id, email) VALUES
(1, 'lvasilevich0@google.co.uk'),
(2, 'hscholey1@sina.com.cn'),
(3, 'mmcjury2@hibu.com');
INSERT INTO transactions21 (user_id, dt, amount) VALUES
(3, '2022-12-05 00:16:56', 162.11),
(1, '2023-05-20 03:20:58', 81.58),
(1, '2023-06-08 19:24:02', 52.46),
(1, '2023-06-27 21:16:07', 447.59),
(1, '2023-07-20 08:19:32', 136.68),
(1, '2023-12-11 17:08:05', 857.55),
(1, '2023-12-15 04:45:54', 77.11),
(1, '2023-12-22 00:46:34', 670.71),
(1, '2023-12-29 12:43:23', 948.46),
(2, '2023-01-04 00:51:46', 793.50),
(2, '2023-04-07 16:29:14', 762.52),
(2, '2023-06-17 17:42:50', 527.18),
(2, '2023-10-10 11:16:51', 733.47),
(2, '2023-10-18 23:32:00', 920.14),
(3, '2023-03-27 18:31:41', 408.13),
(3, '2023-04-08 09:57:55', 817.88),
(3, '2023-05-18 09:47:14', 916.98),
(3, '2023-09-14 14:00:54', 53.30),
(3, '2023-09-30 01:34:01', 589.37),
(3, '2024-01-27 15:13:58', 666.37);
SELECT u.email AS email , COUNT(*) AS total_transactions, 
SUM(t.amount) AS total_amount FROM users21 u 
JOIN transactions21 t ON u.id=t.user_id 
WHERE t.dt LIKE '2023%'
GROUP BY u.email ORDER BY email ASC;
-- or
SELECT 
    u.email AS email,
    COUNT(*) AS total_transactions,
    FORMAT(SUM(t.amount), 2) AS total_amount
FROM users21 u
JOIN transactions21 t 
    ON u.id = t.user_id
WHERE YEAR(STR_TO_DATE(t.dt,'%Y-%m-%d %H:%i:%s'))=2023
GROUP BY u.email
ORDER BY u.email ASC;
-- or
SELECT 
    u.email AS email,
    COUNT(*) AS total_transactions,
    FORMAT(SUM(t.amount), 2) AS total_amount
FROM users21 u
JOIN transactions21 t 
    ON u.id = t.user_id
WHERE EXTRACT(YEAR FROM STR_TO_DATE(t.dt,'%Y-%m-%d %H:%i:%s'))=2023
GROUP BY u.email
ORDER BY u.email ASC;


-- 22
CREATE TABLE coins22 (
  id INT PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE transactions22 (
  coin_id INT,
  dt VARCHAR(19),
  amount DECIMAL(5, 2),
  FOREIGN KEY (coin_id) REFERENCES coins22(id)
);

INSERT INTO coins22 (id, name) VALUES
(1, 'BitCash'),
(2, 'Etherium'),
(3, 'Litecoin'),
(4, 'Ripple'),
(5, 'Dogecoin');
INSERT INTO transactions22 (coin_id, dt, amount) VALUES
(1, '2022-12-09 19:40:17', 60.91),
(1, '2023-01-02 09:35:37', 76.35),
(1, '2023-03-21 09:34:39', 23.11),
(1, '2023-08-11 03:43:27', 80.20),
(1, '2023-10-21 19:42:46', 29.59),
(2, '2023-07-08 19:47:20', 69.49),
(2, '2023-09-22 14:23:40', 23.13),
(3, '2023-01-08 10:22:10', 72.45),
(3, '2023-01-28 00:54:51', 98.72),
(3, '2023-02-24 00:13:32', 70.36),
(3, '2023-05-16 15:13:19', 93.59),
(4, '2023-05-24 13:43:44', 9.34),
(4, '2023-07-25 14:59:09', 78.52),
(5, '2023-01-20 15:49:38', 81.66),
(5, '2023-08-21 17:19:45', 94.89),
(5, '2023-10-25 00:44:42', 64.40),
(5, '2023-11-30 02:38:47', 86.84),
(5, '2023-12-31 03:26:39', 58.99),
(2, '2024-01-21 10:25:26', 29.36),
(5, '2024-01-08 03:09:00', 95.25);
SELECT c.name AS name,ROUND(AVG(t.amount),2) AS avg_transaction_amount 
FROM coins22 c 
JOIN transactions22 t ON c.id=t.coin_id 
WHERE t.dt LIKE '2023%' 
GROUP BY c.name 
ORDER BY avg_transaction_amount ASC LIMIT 3;
 
-- 23
SELECT c.name AS name,COUNT(*) AS total_transaction,MIN(t.amount)AS min_amount,
MAX(t.amount) AS MAx_amount,AVG(t.amount)AS avg_amount FROM coins c JOIN transactions t
ON c.id=t.coin_id WHERE t.dt LIKE '2024-04%' GROUP BY c.name
ORDER BY total_transaction DESC, name ASC;

-- 24
CREATE TABLE suspicious_files (
  filename VARCHAR(255),
  extension VARCHAR(255),
  scan_dt VARCHAR(19),
  is_suspicious INT
);
DROP TABLE IF EXISTS suspicious_files;
INSERT INTO suspicious_files (filename, extension, scan_dt, is_suspicious) VALUES
('Mauris.pdf', '.pdf', '2024-04-05 23:55:27', 1),
('Augue.xls', '.xls', '2024-02-28 18:11:28', 1),
('Sapien.avi', '.avi', '2024-03-30 12:24:10', 1),
('Pulvinar.doc', '.doc', '2024-03-08 22:00:41', 1),
('TemporConvallisNulla.gif', '.gif', '2024-03-29 21:32:41', 1),
('InFaucibus.mp3', '.mp3', '2024-03-20 14:18:32', 1),
('EleifendPedeLibero.ppt', '.ppt', '2024-03-05 04:47:56', 1),
('VestibulumAntelpsum.ppt', '.ppt', '2024-03-05 17:34:34', 1),
('IntegerPece.ppt', '.ppt', '2024-03-12 17:11:28', 1),
('VenenatisNon.tiff', '.tiff', '2024-03-20 18:04:47', 1),
('IaculisDiam.xls', '.xls', '2024-03-01 05:18:03', 1),
('QuisqueArcuLibero.xls', '.xls', '2024-03-09 09:00:32', 1),
('MaurisSic.png', '.png', '2024-04-03 23:20:03', 0),
('SitAmetsm.mp3', '.mp3', '2024-02-23 22:06:43', 0),
('Nisi.mp3', '.mp3', '2024-02-29 09:40:45', 0),
('Magna.tiff', '.tiff', '2024-02-27 00:25:16', 0),
('EratVestibulum.gif', '.gif', '2024-03-30 04:19:52', 0),
('Neque.jpeg', '.jpeg', '2024-03-07 07:11:26', 0),
('VolutpatQuam.ppt', '.ppt', '2024-03-23 04:33:43', 0),
('NonQuam.xls', '.xls', '2024-03-10 19:12:29', 0);
SELECT extension , COUNT(*) AS total_sususpicious_files 
FROM suspicious_files WHERE is_suspicious=1 
AND scan_dt LIKE '2024-03%'
GROUP BY  extension
ORDER BY total_sususpicious_files DESC,extension ASC;
-- 25
SELECT c.email AS email,COUNT(*) AS total_scanned_devices FROM clients c
JOIN devices d ON c.id=d.client_id 
WHERE d.scheduled_scan_dt LIKE '2024-04%' 
GROUP BY email ORDER BY email;
-- 26
CREATE TABLE customers26 (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE site_metrics (
  customer_id INT,
  cpu_usage DECIMAL(5, 2),
  memory_usage DECIMAL(5, 2),
  disk_usage DECIMAL(5, 2),
  FOREIGN KEY (customer_id) REFERENCES customers26(id)
);
INSERT INTO customers26 (id, email) VALUES
(1, 'lrathke0@usa.gov'),
(2, 'epearsall1@fema.gov'),
(3, 'sivasechko2@cisco.com');

INSERT INTO site_metrics (customer_id, cpu_usage, memory_usage, disk_usage) VALUES
(1, 31.53, 80.84, 1.51),
(1, 12.54, 26.47, 47.74),
(1, 12.38, 46.24, 34.43),
(1, 26.64, 84.98, 17.56),
(2, 80.45, 50.05, 10.63),
(2, 40.14, 86.67, 15.98),
(2, 30.14, 34.38, 17.67),
(2, 1.11, 83.44, 2.95),
(3, 30.60, 18.60, 28.02),
(3, 41.64, 33.64, 5.20),
(3, 31.88, 7.37, 91.34),
(3, 43.20, 9.56, 40.40),
(3, 2.33, 34.29, 18.65),
(3, 11.50, 32.99, 71.39),
(3, 39.57, 4.19, 48.05),
(3, 25.06, 23.77, 33.00),
(3, 32.81, 1.59, 25.85),
(3, 48.38, 79.21, 8.31),
(3, 11.62, 26.75, 71.71),
(3, 54.43, 6.46, 4.86);
SELECT c.email AS email,ROUND(AVG(s.cpu_usage),2)AS average_cpu_usage,
ROUND(AVG(s.memory_usage),2)AS average_memory_usage,
ROUND(AVG(s.disk_usage),2)AS average_disk_usage FROM customers26 c 
JOIN site_metrics s
ON c.id=s.customer_id GROUP BY email HAVING AVG(s.cpu_usage)>50 
OR AVG(s.memory_usage)>50 OR AVG(s.disk_usage)>50
ORDER BY email ASC;

-- 27
CREATE TABLE customers27 (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE sites (
  customer_id INT,
  url VARCHAR(255),
  is_active INT,
  FOREIGN KEY (customer_id) REFERENCES customers27(id)
);
DROP TABLE IF EXISTS sites;
INSERT INTO customers27 (id, email) VALUES
(1, 'dcristofol0@slashdot.org'),
(2, 'mbillanie1@japanpost.jp'),
(3, 'hmainz2@utexas.edu');
INSERT INTO sites (customer_id, url, is_active) VALUES
(1, 'https://reuters.com', 1),
(1, 'https://www.google.de', 1),
(1, 'https://merriam-webster.com', 1),
(1, 'https://wordpress.com', 1),
(1, 'https://www.gov.au', 1),
(1, 'https://www.barnesandnoble.com', 1),
(1, 'https://www.yahoo.com', 1),
(2, 'https://cloudflare.com', 0),
(2, 'https://www.drupal.org', 1),
(2, 'https://www.unesco.org', 1),
(3, 'https://www.sina.com.cn', 0),
(3, 'https://xinhuanet.com', 1),
(3, 'https://cybershims.com', 1),
(3, 'https://eaa.com', 1),
(3, 'https://businessinsider.com', 1),
(3, 'https://www.daily.co.uk', 1),
(3, 'https://www.guardian.co.uk', 1),
(3, 'https://www.microsoft.com', 1),
(3, 'https://www.armada6.com', 1),
(3, 'https://www.163.com', 1);

SELECT c.email AS email,SUM(s.is_active)AS total_active_sites 
FROM customers27 c
INNER JOIN sites s ON c.id=s.customer_id WHERE s.is_active IS TRUE
GROUP BY email ORDER BY email ASC;

-- 28
-- Drop tables first
DROP TABLE IF EXISTS income28;
DROP TABLE IF EXISTS accounts28;

-- Create tables
CREATE TABLE accounts28 (
  id INT PRIMARY KEY,
  iban VARCHAR(255)
);

CREATE TABLE income28 (
  account_id INT,
  dt DATETIME,
  amount DECIMAL(10,2),
  FOREIGN KEY (account_id) REFERENCES accounts28(id)
);

-- Insert accounts
INSERT INTO accounts28 (id, iban) VALUES
(1, 'FR55 4477 6154 73ND TN3F HMOU T36'),
(2, 'DK46 1272 1831 2573 01'),
(3, 'RS53 5237 5794 6016 5411 43'),
(4, 'GB29 NWBK 6016 1331 9268 19'),
(5, 'NL91 ABNA 0417 1643 00');

-- Insert income
INSERT INTO income28 (account_id, dt, amount) VALUES
(1, '2024-01-17 16:43:20', 4061.53),
(1, '2024-02-28 05:30:15', 4488.11),
(1, '2024-04-07 05:41:27', 4001.91),
(2, '2023-12-21 07:38:45', 4313.69),
(2, '2024-01-08 04:48:45', 3640.82),
(2, '2024-01-20 17:31:20', 3385.15),
(3, '2024-01-06 23:18:30', 2347.15),
(3, '2024-03-08 12:53:20', 3814.86),
(3, '2024-04-01 21:18:16', 2764.27),
(4, '2024-01-02 23:52:06', 3526.08),
(4, '2024-02-04 12:32:28', 2221.91),
(4, '2024-02-11 19:44:53', 4197.07),
(4, '2024-03-06 06:28:34', 1357.44),
(4, '2024-03-16 16:13:49', 1854.52),
(5, '2023-12-31 22:08:57', 2819.54),
(5, '2024-01-14 18:03:47', 2641.20),
(5, '2024-01-23 07:50:22', 3692.56),
(5, '2024-02-28 23:43:28', 1999.09),
(5, '2024-03-20 10:29:44', 1670.18),
(5, '2024-03-27 11:12:04', 1193.15);

SELECT a.iban AS iban,ROUND(AVG(i.amount),2)AS average_income,SUM(i.amount) AS total_income
FROM accounts28 a JOIN income28 i ON a.id=i.account_id
WHERE DATE(i.dt) >DATE('2024-01-01') AND DATE(i.dt)<DATE('2024-03-31')
GROUP BY iban ORDER BY average_income DESC,iban ASC LIMIT 3;

-- 29
-- Create accounts table
CREATE TABLE accounts29 (
    id INT PRIMARY KEY,                   -- The identifier of the account
    iban VARCHAR(255)                     -- The IBAN of the account
);

-- Create income table
CREATE TABLE income29 (
    account_id INT,                       -- The reference to the account
    dt VARCHAR(19),                       -- The date and time of income
    amount DECIMAL(6,2),                  -- The income amount
    FOREIGN KEY (account_id) REFERENCES accounts29(id)
);

INSERT INTO accounts29 (id, iban) VALUES
(1, 'FR55 4477 6154 73ND TN3F HMOU T36'),
(2, 'DK46 1272 1831 2573 01'),
(3, 'RS53 5237 5794 6016 5411 43');
INSERT INTO income29 (account_id, dt, amount) VALUES
(1, '2022-12-31 10:03:42', 2779.19),
(1, '2023-02-04 08:50:14', 1777.68),
(1, '2023-02-13 04:22:07', 1954.81),
(1, '2023-03-04 14:46:04', 1547.79),
(1, '2023-05-23 15:42:13', 1208.49),
(1, '2023-05-24 23:24:07', 1521.72),
(1, '2023-07-28 11:00:46', 1792.75),
(1, '2023-12-07 14:19:09', 2374.25),
(1, '2024-01-27 05:55:36', 2803.39),
(2, '2022-12-03 18:30:44', 1826.65),
(2, '2023-02-17 00:59:57', 3074.11),
(2, '2023-03-01 08:17:15', 1007.30),
(2, '2023-08-19 09:16:41', 4515.04),
(2, '2024-01-08 04:14:22', 3321.78),
(2, '2024-01-10 15:16:28', 2033.87),
(3, '2023-05-09 07:28:27', 3158.66),
(3, '2023-05-22 04:39:34', 3851.20),
(3, '2023-07-21 19:51:14', 4152.29),
(3, '2023-10-05 05:42:49', 4722.20),
(3, '2023-11-11 02:42:59', 1592.16);


SELECT a.iban AS iban,SUM(i.amount)AS total_income,
'20%'AS tax_rate,
FORMAT(SUM(i.amount)*0.2,2) AS calculated_tax
FROM accounts29 a JOIN income29 i ON a.id=i.account_id 
WHERE i.dt LIKE '2023%' GROUP BY iban ORDER BY iban ASC;
-- 30
CREATE TABLE customers30(
  id INT PRIMARY KEY,
  email VARCHAR(255)
);

CREATE TABLE expenses (
  customer_id INT,
  dt VARCHAR(19),
  amount DECIMAL(6, 2),
  FOREIGN KEY (customer_id) REFERENCES customers30(id)
);

CREATE TABLE income30 (
  customer_id INT,
  dt VARCHAR(19),
  amount DECIMAL(6, 2),
  FOREIGN KEY (customer_id) REFERENCES customers30(id)
);

INSERT INTO customers30 (id, email) VALUES
(1, 'otoohey0@elpais.com'),
(2, 'egrebbin1@state.gov'),
(3, 'arides2@sohu.com');

INSERT INTO expenses (customer_id, dt, amount) VALUES
(1, '2024-02-21 22:12:12', 90.41),
(1, '2024-02-27 05:48:37', 792.88),
(1, '2024-03-10 05:19:43', 442.01),
(1, '2024-03-11 19:48:25', 327.35),
(1, '2024-03-24 22:03:06', 639.62),
(1, '2024-03-29 00:37:46', 150.12),
(1, '2024-04-02 03:36:50', 257.67),
(2, '2024-02-21 06:11:26', 400.22),
(2, '2024-03-11 15:34:19', 298.41),
(2, '2024-03-25 04:36:27', 376.87),
(2, '2024-03-29 19:05:51', 530.07),
(2, '2024-03-30 07:07:28', 287.84),
(2, '2024-03-02 15:46:12', 198.81),
(2, '2024-03-06 10:07:47', 73.30),
(2, '2024-03-20 00:11:58', 398.11),
(3, '2024-03-16 23:54:48', 968.05),
(3, '2024-03-25 15:19:33', 70.55),
(3, '2024-03-31 06:51:13', 198.17),
(3, '2024-04-03 12:11:56', 836.82),
(3, '2024-03-31 17:54:37', 140.99);

INSERT INTO income30 (customer_id, dt, amount) VALUES
(1, '2024-02-21 00:23:05', 766.66),
(1, '2024-03-11 03:23:04', 709.18),
(1, '2024-03-15 20:49:55', 84.38),
(1, '2024-03-24 15:32:51', 199.48),
(1, '2024-03-29 11:34:13', 733.95),
(1, '2024-04-01 22:34:04', 15.69),
(1, '2024-04-02 11:45:49', 203.16),
(2, '2024-03-29 14:02:15', 212.78),
(2, '2024-03-30 19:57:32', 272.21),
(2, '2024-04-09 23:56:47', 24.08),
(3, '2024-03-20 16:54:34', 898.34),
(3, '2024-03-26 22:28:28', 936.14),
(3, '2024-03-29 10:23:33', 418.82),
(3, '2024-03-29 08:25:31', 462.85),
(3, '2024-03-01 02:18:42', 562.00),
(3, '2024-03-04 12:27:34', 30.21),
(3, '2024-03-19 12:12:09', 60.00),
(3, '2024-03-21 00:32:19', 674.76),
(3, '2024-03-23 14:14:31', 463.39),
(3, '2024-04-08 13:37:03', 51.42);

SELECT c.email AS email,ROUND(SUM(e.amount),2) AS total_expenses,
ROUND(SUM(i.amount),2) AS total_income
FROM customers30 c JOIN expenses e ON c.id=e.customer_id
JOIN income30 i ON c.id=i.customer_id
WHERE e.dt LIKE '2024-03%' AND i.dt LIKE '2024-03%'
 GROUP BY email ORDER BY email ASC;
 -- or
SELECT 
  c.email AS email,
  COALESCE(ROUND(e.total_expenses, 2), 0.00) AS total_expenses,
  COALESCE(ROUND(i.total_income, 2), 0.00) AS total_income
FROM customers30 c
LEFT JOIN (
    SELECT customer_id, SUM(amount) AS total_expenses
    FROM expenses
    WHERE dt LIKE '2024-03%'
    GROUP BY customer_id
) e ON c.id = e.customer_id
LEFT JOIN (
    SELECT customer_id, SUM(amount) AS total_income
    FROM income30
    WHERE dt LIKE '2024-03%'
    GROUP BY customer_id
) i ON c.id = i.customer_id
ORDER BY c.email ASC;

-- 31
CREATE TABLE customers31 (
    id INT PRIMARY KEY,
    email VARCHAR(255)
);

CREATE TABLE expenses31 (
    customer_id INT,
    amount DECIMAL(6,2),
    FOREIGN KEY (customer_id) REFERENCES customers31(id)
);

CREATE TABLE income31 (
    customer_id INT,
    amount DECIMAL(6,2),
    FOREIGN KEY (customer_id) REFERENCES customers31(id)
);


INSERT INTO customers31 VALUES
(1, 'dtollmache0@typepad.com'),
(2, 'eclutterbuck1@baidu.com'),
(3, 'mdensun2@ustream.tv');

-- Sample expenses
INSERT INTO expenses31 VALUES
(1, 136.18),
(1, 323.28),
(1, 383.37),
(1, 505.41),
(1, 841.21),
(2, 5.23),
(2, 408.33),
(2, 489.45),
(2, 545.40),
(2, 591.43),
(2, 706.13),
(2, 716.82),
(2, 761.75),
(2, 796.30),
(3, 152.26),
(3, 211.30),
(3, 447.57),
(3, 685.03),
(3, 966.89),
(3, 967.30);

-- Sample income
INSERT INTO income31 VALUES
(1, 39.44),
(1, 49.49),
(1, 292.19),
(1, 419.36),
(1, 529.26),
(1, 695.43),
(1, 763.72),
(1, 797.92),
(1, 833.34),
(2, 139.42),
(2, 422.18),
(2, 506.59),
(2, 566.00),
(2, 697.92),
(2, 938.51),
(3, 304.66),
(3, 345.03),
(3, 371.86),
(3, 371.88),
(3, 552.08);

SELECT c.email AS email,SUM(i.amount)-SUM(e.amount)AS balance 
FROM customers31 c
JOIN expenses31 e ON c.id=e.customer_id
JOIN income31 i ON c.id=i.customer_id
GROUP BY email
HAVING (SUM(i.amount)-SUM(e.amount))<0 ORDER BY email ASC;
-------------------------------------
-- 32
CREATE TABLE products32 (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE sales32 (
    product_id INT,
    dt VARCHAR(19),
    amount DECIMAL(7,2),
    FOREIGN KEY (product_id) REFERENCES products32(id)
);
INSERT INTO products32 VALUES
(1, 'Luxury Gold Watch'),
(2, 'Smartphone Holder Stand'),
(3, 'Stainless Steel Water Bottle');

INSERT INTO sales32 VALUES
(1, '2024-01-13 17:12:22', 7008.16),
(1, '2024-01-03 03:15:27', 6191.64),
(1, '2024-01-22 18:29:09', 4527.86),
(1, '2024-01-26 19:38:53', 7828.36),
(1, '2024-02-17 09:27:13', 5273.16),
(1, '2024-02-11 09:51:24', 3364.73),
(1, '2024-02-22 23:53:15', 8584.33),
(2, '2024-01-28 11:33:58', 3710.06),
(2, '2024-01-25 14:47:25', 5221.02),
(2, '2024-01-21 07:58:53', 2525.72),
(2, '2024-03-15 14:16:18', 8158.08),
(2, '2024-03-12 17:02:01', 6760.77),
(3, '2024-01-13 19:27:51', 1942.79),
(3, '2024-02-15 08:04:40', 9186.38),
(3, '2024-03-06 08:02:37', 5821.97),
(3, '2024-03-03 15:39:18', 8676.24);


SELECT p.name AS name,MONTHNAME(STR_TO_DATE(s.dt,'%Y-%m-%d %H:%i:%s'))
AS month,
ROUND(SUM(s.amount),2)AS total_sales
FROM products32 p JOIN sales32 s ON p.id=s.product_id
WHERE STR_TO_DATE(s.dt,'%Y-%m-%d %H:%i:%s') 
BETWEEN '2024-01-01' AND '2024-03-31'
GROUP BY p.name,MONTH(STR_TO_DATE(s.dt,'%Y-%m-%d %H:%i:%s')),
MONTHNAME(STR_TO_DATE(s.dt,'%Y-%m-%d %H:%i:%s'))
ORDER BY MONTH(STR_TO_DATE(s.dt,'%Y-%m-%d %H:%i:%s')) ASC,
total_sales DESC;
 
 SELECT 
  p.name AS name,
  MONTHNAME(STR_TO_DATE(s.dt, '%Y-%m-%d %H:%i:%s')) AS month,
  ROUND(SUM(s.amount), 2) AS total_sales
FROM products32 p
JOIN sales32 s ON p.id = s.product_id
WHERE STR_TO_DATE(s.dt, '%Y-%m-%d %H:%i:%s') BETWEEN '2024-01-01' AND '2024-03-31'
GROUP BY 
  p.name, 
  MONTH(STR_TO_DATE(s.dt, '%Y-%m-%d %H:%i:%s')), 
  MONTHNAME(STR_TO_DATE(s.dt, '%Y-%m-%d %H:%i:%s'))
ORDER BY 
  MONTH(STR_TO_DATE(s.dt, '%Y-%m-%d %H:%i:%s')) ASC, 
  total_sales DESC;

-------------------------------------------------------
-- 33
CREATE TABLE projects33 (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE employees33 (
    id INT PRIMARY KEY,
    ein VARCHAR(255),
    experience_years INT
);

CREATE TABLE projects_employees (
    project_id INT,
    employee_id INT,
    FOREIGN KEY (project_id) REFERENCES projects33(id),
    FOREIGN KEY (employee_id) REFERENCES employees33(id)
);

INSERT INTO projects33 VALUES
(1, 'Project X'),
(2, 'Sunshine Project'),
(3, 'Blue Sky Initiative');

INSERT INTO employees33 VALUES
(1, '62-0524667', 4),
(2, '62-1435366', 1),
(3, '29-3144922', 1),
(4, '80-9606443', 1),
(5, '63-6630813', 1);

INSERT INTO projects_employees VALUES
(1, 1), (1, 1), (1, 2), (1, 3), (1, 5),
(2, 1), (2, 1), (2, 2), (2, 5),
(3, 1), (3, 1), (3, 2), (3, 3), (3, 3), (3, 4), (3, 4), (3, 5), (3, 5), (3, 5), (3, 5);

SELECT p.name AS project_name,COUNT(*)AS employee_count,
ROUND(AVG(e.experience_years)) AS avg_experience,
CASE
	WHEN COUNT(*)<5 THEN 'YES'
	ELSE 'NO'
END AS is_understaffed FROM projects_employees pe 
JOIN projects33 p ON pe.project_id=p.id
JOIN employees33 e ON pe.employee_id=e.id 
GROUP BY p.name HAVING AVG(e.experience_years)>2
ORDER BY employee_count DESC,project_name ASC;

-----------------------------------------------
-- 34
CREATE TABLE transactions34 (
    dt VARCHAR(19),
    wallet VARCHAR(255),
    amount DECIMAL(4,2)
);

INSERT INTO transactions34 VALUES
('2024-02-28 06:20:04', '0x1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c', -7.36),
('2024-02-12 07:45:28', '0x1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c', -3.71),
('2024-02-25 10:49:54', '0x1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c', -3.53),
('2024-02-03 19:43:00', '0x1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c', 4.01),
('2024-02-14 08:55:30', '0x1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c', 8.20),
('2024-02-16 04:31:26', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', -8.96),
('2024-02-06 23:45:31', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', -7.88),
('2024-02-11 01:00:35', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', -7.66),
('2024-02-25 09:39:01', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', -7.45),
('2024-02-14 04:04:15', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', 4.17),
('2024-02-15 11:47:23', '0x3a4FbC5Df2E1bBfDdE5c4fA7bF8dE7aC1F', 7.56),
('2024-02-24 14:58:54', '0x9B8aDc2eFf4cC3DdEe5f6a7B8dE9aC1F', -1.45),
('2024-02-18 21:17:24', '0x9B8aDc2eFf4cC3DdEe5f6a7B8dE9aC1F', 1.05),
('2024-02-19 11:12:32', '0x9B8aDc2eFf4cC3DdEe5f6a7B8dE9aC1F', 3.67);
SELECT wallet,COUNT(*) AS total_transactions,
ROUND(SUM(CASE WHEN amount>0 THEN amount ELSE 0 END),2) AS total_bought,
ROUND(SUM(CASE WHEN amount<0 THEN amount ELSE 0 END),2) AS total_sold
 FROM transactions34 WHERE dt LIKE '2024-02%' GROUP BY wallet ORDER BY wallet;
---------------------------------------------------------------------------
-- 35
CREATE TABLE employees35 (
    id INT PRIMARY KEY,
    email VARCHAR(255)
);

CREATE TABLE leave_records35 (
    employee_id INT,
    leave_dt VARCHAR(19),
    days_taken INT,
    FOREIGN KEY (employee_id) REFERENCES employees35(id)
);
INSERT INTO employees35 VALUES
(1, 'jquartly0@macromedia.com'),
(2, 'cchastand1@stanford.edu'),
(3, 'lpuckrin2@creativecommons.org');
DROP TABLE IF EXISTS leave_records35;

INSERT INTO leave_records35 (employee_id, leave_dt, days_taken) VALUES
(1, '2022-11-10 11:52:14', 4),
(1, '2022-09-07 23:22:46', 1),
(2, '2022-11-11 01:47:50', 7),
(3, '2022-11-06 23:12:27', 7),
(3, '2022-11-17 07:43:18', 7),
(1, '2023-05-19 04:40:25', 2),
(1, '2023-12-25 16:29:51', 7),
(1, '2023-03-12 18:54:29', 1),
(1, '2023-08-23 12:33:56', 6),
(2, '2023-04-20 04:19:10', 5),
(2, '2023-04-28 00:41:50', 7),
(3, '2023-06-11 18:49:25', 2),
(3, '2023-12-23 15:53:10', 7),
(3, '2023-03-13 13:46:16', 2),
(3, '2023-10-08 11:57:43', 2),
(3, '2023-04-12 07:49:02', 4),
(3, '2023-01-17 06:05:35', 6),
(1, '2024-02-05 16:01:59', 1),
(1, '2024-01-05 22:15:30', 7),
(2, '2024-02-21 00:50:11', 4);
SELECT e.email AS email,SUM(l.days_taken)AS leave_days_taken,
CASE
	WHEN SUM(l.days_taken)<20 THEN 'WITHIN LIMIT'
	ELSE 'EXCEEDED'
END
AS leave_status
FROM employees35 e JOIN leave_records35 l 
ON e.id=l.employee_id WHERE l.leave_dt LIKE '2023%'
GROUP BY email ORDER BY email ASC;

-------------------------------------------------
-- 36
CREATE TABLE campaigns36 (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE email_stats36 (
    campaign_id INT,
    emails_sent INT,
    emails_opened INT,
    FOREIGN KEY (campaign_id) REFERENCES campaigns36(id)
);

INSERT INTO campaigns36 VALUES
(1, 'SummerSale2021'),
(2, 'FallPromo'),
(3, 'WinterWonderland');

INSERT INTO email_stats36 VALUES
(1, 1749, 775), (1, 641, 423), (1, 976, 598), (1, 756, 121),
(1, 975, 107), (1, 752, 367), (1, 1068, 809), (1, 1046, 589),
(1, 1212, 939), (1, 567, 214), (2, 1084, 283), (2, 992, 478),
(2, 1505, 604), (3, 899, 315), (3, 742, 554), (3, 1744, 917),
(3, 1163, 423), (3, 1501, 948), (3, 736, 451), (3, 537, 434);

SELECT c.name AS name,SUM(e.emails_sent)AS total_emails_sent,
SUM(e.emails_opened)AS total_emails_opened,
ROUND(SUM(e.emails_opened)*100.0/SUM(e.emails_sent),2)
AS open_rate FROM campaigns36 c JOIN email_stats36 e
ON c.id=e.campaign_id GROUP BY c.name 
HAVING ROUND(SUM(e.emails_opened)*100.0/SUM(e.emails_sent),2)>50 
ORDER BY open_rate DESC ,name ASC;

---------------------------------
-- 37
CREATE TABLE bonds37 (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE maturities37 (
    bond_id INT,
    maturity DATE,
    FOREIGN KEY (bond_id) REFERENCES bonds37(id)
);

INSERT INTO bonds37 VALUES
(1, 'Alpha Mortgage Bond'),
(2, 'Beta Mortgage Bond'),
(3, 'Gamma Mortgage Bond');

INSERT INTO maturities37 VALUES
(1, '2024-01-26'), (1, '2024-02-22'), (1, '2024-03-26'), (1, '2024-05-13'),
(1, '2024-07-06'), (1, '2024-08-23'), (1, '2024-09-06'), (1, '2024-11-30'),
(1, '2024-12-30'), (1, '2025-04-30'), (1, '2025-05-03'), (2, '2024-07-25'),
(2, '2024-12-07'), (3, '2023-12-16'), (3, '2024-01-25'), (3, '2024-01-26'),
(3, '2024-05-04'), (3, '2024-10-02'), (3, '2024-12-14'), (3, '2025-01-15');

SELECT b.name AS name,COUNT(*)AS maturity_dates,
MIN(m.maturity) AS earliest_maturity,
MAX(m.maturity) AS latest_maturity,
CEIL(AVG(DATEDIFF(m.maturity,'2023-09-13')))AS avg_maturity
FROM bonds37 b JOIN maturities37 m ON b.id=m.bond_id 
GROUP BY b.name 
HAVING AVG(DATEDIFF(m.maturity,'2023-09-13'))>365
 ORDER BY name ASC;
-------------------------------------
-- 38
CREATE TABLE bonds (
    id INT PRIMARY KEY,       
    name VARCHAR(255)         
);
CREATE TABLE interest_rates (
    bond_id INT,
    rate DECIMAL(2,1),
    FOREIGN KEY (bond_id) REFERENCES bonds(id)
);
INSERT INTO bonds (id, name) VALUES
(1, 'Alpha Mortgage Bond'),
(2, 'Beta Mortgage Bond'),
(3, 'Gamma Mortgage Bond');

INSERT INTO interest_rates VALUES
(1, 1.4), (1, 1.8), (1, 2.0), (1, 2.4), (1, 3.4), (1, 4.6), (1, 4.7), (1, 4.9),
(2, 2.0), (2, 2.1), (2, 3.0), (2, 3.2), (2, 4.0),
(3, 1.2), (3, 1.3), (3, 1.4), (3, 2.1), (3, 2.5), (3, 3.5), (3, 4.0);

SELECT b.name AS name,ROUND(COUNT(i.rate))AS interest_rates,
MIN(i.rate)AS lowest_rate,MAX(i.rate)AS highest_rate,
ROUND(AVG(i.rate),2)AS avg_rate FROM bonds b JOIN interest_rates i 
ON b.id=i.bond_id GROUP BY b.name HAVING AVG(i.rate)>3 
ORDER BY name ASC;

-------------------------------------
-- 39
CREATE TABLE bondholders (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE bonds39 (
    id INT PRIMARY KEY,
    name VARCHAR(255),
    annual_coupon DECIMAL(5,2),
    coupons_remaining INT
);

CREATE TABLE bondholders_bonds (
    bondholder_id INT,
    bond_id INT,
    FOREIGN KEY (bondholder_id) REFERENCES bondholders(id),
    FOREIGN KEY (bond_id) REFERENCES bonds39(id)
);
INSERT INTO bondholders VALUES
(1, 'Alex Smith'), (2, 'Taylor Johnson'), (3, 'Jordan Davis');

INSERT INTO bonds39 VALUES
(1, 'Golden Bonds', 150.00, 4), (2, 'Silver Lining', 200.00, 2),
(3, 'Diamond Trust', 100.00, 4), (4, 'Emerald Wealth', 350.00, 5),
(5, 'Ruby Returns', 150.00, 8), (6, 'Sapphire Security', 450.00, 5),
(7, 'Amber Assurance', 100.00, 8), (8, 'Topaz Treasury', 100.00, 2),
(9, 'Opal Opportunities', 150.00, 5), (10, 'Pearl Prosperity', 450.00, 5),
(11, 'Platinum Promise', 450.00, 9), (12, 'Jade Investments', 350.00, 1),
(13, 'Garnet Growth', 150.00, 4), (14, 'Onyx Returns', 350.00, 2),
(15, 'Quartz Capital', 100.00, 2), (16, 'Citrine Securities', 250.00, 2),
(17, 'Aquamarine Assets', 250.00, 2), (18, 'Peridot Portfolio', 300.00, 8),
(19, 'Tourmaline Trust', 100.00, 6), (20, 'Moonstone Money', 150.00, 9);

INSERT INTO bondholders_bonds VALUES
(1, 1), (1, 2), (1, 6), (1, 8), (1, 9), (1, 13), (1, 14), (1, 16), (1, 17),
(2, 4), (2, 5), (2, 7), (2, 11), (2, 15), (2, 18),
(3, 3), (3, 10), (3, 12), (3, 19), (3, 20);
SELECT b1.name AS name,
SUM(b2.annual_coupon*b2.coupons_remaining) AS total_cash_flow FROM bondholders_bonds bb
JOIN bondholders b1 ON bb.bondholder_id=b1.id
JOIN bonds39 b2 ON bb.bond_id=b2.id 
GROUP BY b1.name HAVING SUM(b2.annual_coupon*b2.coupons_remaining)>10000 
ORDER BY total_cash_flow DESC;
----------------------------------------
-- 40
CREATE TABLE investors (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);

CREATE TABLE cash_flows (
    investor_id INT,
    cash_flow DECIMAL(8,2),
    FOREIGN KEY (investor_id) REFERENCES investors(id)
);
INSERT INTO investors VALUES
(1, 'ematson0@ebay.co.uk'),
(2, 'lsalvadore1@msn.com'),
(3, 'aclowser2@patch.com');
DROP TABLE IF EXISTS cash_flows;
INSERT INTO cash_flows (investor_id, cash_flow) VALUES
(1, 184040.12),
(1, 179280.08),
(1, 179374.42),
(1, 79302.21),
(1, 87466.20),
(1, 194588.36),
(1, 153563.92),
(1, 56377.92),
(2, 59039.14),
(2, 167247.23),
(2, 59311.03),
(2, 183883.00),
(2, 118851.21),
(3, 58868.62),
(3, 96909.26),
(3, 103735.73),
(3, 171261.97),
(3, 86463.11),
(3, 56931.73),
(3, 194699.58);

SELECT i.email AS email,COUNT(*)AS investments,
MIN(c.cash_flow)AS min_cash_flow,MAX(c.cash_flow)AS max_cash_flow,
ROUND(AVG(c.cash_flow),2)AS avg_cash_flow FROM investors i JOIN cash_flows c
ON i.id=c.investor_id GROUP BY email 
HAVING SUM(c.cash_flow)>1000000 ORDER BY email ASC;
----------------------------------------------------

-- 41
DROP TABLE IF EXISTS investors41;
  CREATE TABLE investors41 (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);

CREATE TABLE cash_flows41 (
    investor_id INT,
    expected_flow DECIMAL(8,2),
    FOREIGN KEY (investor_id) REFERENCES investors41(id)
);

INSERT INTO investors41 (id, email) VALUES
(1, 'tdowner0@timesonline.co.uk'),
(2, 'cgarza1@opera.com'),
(3, 'nbarwise2@si.edu');
INSERT INTO cash_flows41 (investor_id, expected_flow) VALUES
(1, 24923.83),
(1, 30212.10),
(1, 87126.50),
(1, 56018.65),
(1, 93357.47),
(1, 55073.54),
(1, 27095.07),
(2, 44165.12),
(2, 43658.84),
(2, 35835.34),
(2, 12660.46),
(2, 58676.60),
(2, 95929.25),
(2, 47161.23),
(2, 80283.91),
(2, 54427.20),
(2, 93223.98),
(3, 19741.35);

SELECT i.email AS email,
COUNT(*)AS investment_count,SUM(c.expected_flow)AS total_expected_flow,
MAX(c.expected_flow)-MIN(c.expected_flow)AS range_expected_flow FROM investors41 i
JOIN cash_flows41 c ON i.id=c.investor_id
GROUP BY i.id,email HAVING  SUM(c.expected_flow)>100000
ORDER BY email ASC;

-- 42
CREATE TABLE coupons42 (
    id INT PRIMARY KEY,
    coupon_code VARCHAR(255) UNIQUE,
    description VARCHAR(255),
    is_enabled SMALLINT
);

CREATE TABLE coupon_uses42 (
    coupon_id INT,
    amount DECIMAL(4,2),
    FOREIGN KEY (coupon_id) REFERENCES coupons42(id)
);
INSERT INTO coupons42 (id, coupon_code, description, is_enabled) VALUES
(1, 'COUPON123', 'nisi nam ultrices libero non', 0),
(2, 'SAVE20', 'ac est lacinia', 1),
(3, 'DISCOUNT50', 'quis odio consequat', 1);


INSERT INTO coupon_uses42 (coupon_id, amount) VALUES
(1, 36.68),
(1, 3.56),
(1, 2.10),
(1, 39.58),
(2, 39.81),
(2, 24.07),
(2, 28.42),
(2, 31.03),
(2, 3.24),
(2, 36.33),
(3, 8.89),
(3, 30.44),
(3, 36.94),
(3, 42.65),
(3, 33.61),
(3, 41.92),
(3, 1.78),
(3, 20.26),
(3, 27.92),
(3,0.23);

SELECT c.coupon_code AS coupon_code,c.description AS description,
COUNT(*)AS total_uses,MIN(cu.amount)AS min_discount,
MAX(cu.amount)AS max_discount,ROUND(AVG(cu.amount),2)AS avg_discount 
FROM coupons42 c
JOIN coupon_uses42 cu ON c.id=cu.coupon_id 
WHERE c.is_enabled =1
GROUP BY c.id
ORDER BY coupon_code ASC;

-- 43
DROP TABLE IF EXISTS professions43;
DROP TABLE IF EXISTS freelancers43;
DROP TABLE IF EXISTS projects43;

CREATE TABLE professions43 (
    id INT PRIMARY KEY,
    title VARCHAR(255)
);

CREATE TABLE freelancers43 (
    id INT PRIMARY KEY,
    profession_id INT,
    email VARCHAR(255),
    FOREIGN KEY (profession_id) REFERENCES professions43(id)
);

CREATE TABLE projects43 (
    id INT PRIMARY KEY,
    freelancer_id INT,
    status ENUM('Completed', 'Ongoing', 'Cancelled'),
    income DECIMAL(10,2),
    FOREIGN KEY (freelancer_id) REFERENCES freelancers43(id)
);



INSERT INTO professions43 (id, title) VALUES
(1, 'Artificial Intelligence Engineer'),
(2, 'Network Administrator'),
(3, 'Game Developer');

INSERT INTO freelancers43 (id, profession_id, email) VALUES
(1, 1, 'lfernez0@microsoft.com'),
(3, 2, 'mbrydone2@delicious.com'),
(4, 2, 'jhamp3@4shared.com'),
(5, 3, 'cparfett4@twitter.com');

INSERT INTO projects43 (id, freelancer_id, status, income) VALUES
(5, 1, 'Completed', 8562.13),
(11, 1, 'Completed', 6727.56),
(10, 3, 'Completed', 3753.46),
(20, 3, 'Completed', 6659.39),
(6, 4, 'Completed', 8459.28),
(13, 4, 'Completed', 5899.31),
(16, 4, 'Completed', 2709.63),
(4, 5, 'Completed', 5029.44),
(7, 5, 'Completed', 1763.94),
(9, 5, 'Completed', 6988.36),
(8, 3, 'Cancelled', 8699.67),
(1, 5, 'Cancelled', 5403.21),
(19, 3, 'Ongoing', 72.51),
(3, 4, 'Ongoing', 8561.14),
(15, 4, 'Ongoing', 9235.78),
(17, 4, 'Ongoing', 4307.76);
SELECT p.title AS title,COUNT(p1.id)AS total_projects,
SUM(p1.income)AS total_income,COUNT(DISTINCT f.id)AS total_feeelancers,
AVG(p1.income)AS average_income_per_freelancer FROM professions43 p
JOIN freelancers43 f ON p.id=f.profession_id 
JOIN projects43 p1 ON f.id=p1.freelancer_id
WHERE p1.status='Completed'GROUP BY title
ORDER BY total_income DESC;

-- 44
DROP TABLE IF EXISTS categories44;
CREATE TABLE categories44 (
    id INT PRIMARY KEY,
    title VARCHAR(255) UNIQUE
);

CREATE TABLE products44 (
    id INT PRIMARY KEY,
    category_id INT,
    title VARCHAR(255),
    sku VARCHAR(255) UNIQUE,
    stock_number INT,
    FOREIGN KEY (category_id) REFERENCES categories44(id)
);

INSERT INTO categories44 (id, title) VALUES
(1, 'Electronics'),
(2, 'Clothing'),
(3, 'Home & Kitchen');


INSERT INTO products44 (id, category_id, title, sku, stock_number) VALUES
(11, 1, 'Elegant Gadget', 'EG-11', 4),
(3, 1, 'Luxury Gizmo', 'LG-3', 10),
(19, 1, 'Sleek Widget', 'SW-19', 8),
(8, 1, 'Sleek Widget', 'SW-8', 8),
(14, 2, 'Elegant Gadget', 'EG-14', 2),
(16, 2, 'Elegant Gadget', 'EG-16', 6),
(10, 2, 'Elegant Gadget', 'EG-10', 10),
(7, 2, 'Luxury Gizmo', 'LG-7', 3),
(2, 2, 'Luxury Gizmo', 'LG-2', 8),
(18, 2, 'Luxury Gizmo', 'LG-18', 9),
(1, 2, 'Sleek Widget', 'SW-1', 3),
(6, 2, 'Sleek Widget', 'SW-6', 7),
(20, 3, 'Elegant Gadget', 'EG-20', 10),
(9, 3, 'Luxury Gizmo', 'LG-9', 4),
(12, 3, 'Luxury Gizmo', 'LG-12', 5),
(13, 3, 'Luxury Gizmo', 'LG-13', 5),
(5, 3, 'Luxury Gizmo', 'LG-5', 9),
(4, 3, 'Sleek Widget', 'SW-4', 8),
(15, 3, 'Sleek Widget', 'SW-15', 9),
(17, 3, 'Sleek Widget', 'SW-17', 9);
SELECT c.title AS category,p.title AS title,
SUM(p.stock_number) AS total_stock 
FROM categories44 c JOIN products44 p
ON c.id=p.category_id GROUP BY c.title,p.title HAVING SUM(p.stock_number)>10
ORDER BY category ASC,title ASC,total_stock DESC;
-- 45
CREATE TABLE threat_types45 (
    id INT PRIMARY KEY,
    threat_type VARCHAR(255)
);
CREATE TABLE quarantine_urls45 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    threat_id INT,
    domain_name VARCHAR(255) NOT NULL,
    status ENUM('Quarantined', 'Safe', 'Deleted'),
    users_affected INT,
    FOREIGN KEY (threat_id) REFERENCES threat_types45(id)
);


INSERT INTO threat_types45 (id, threat_type) VALUES
(1, 'Phishing'),
(2, 'Rootkit'),
(3, 'Malware');

INSERT INTO quarantine_urls45 (id, threat_id, domain_name, status, users_affected) VALUES
(17, 1, 'amazon.com', 'Quarantined', 862),
(16, 1, 'google.com', 'Quarantined', 63),
(9, 1, 'amazon.com', 'Quarantined', 41),
(18, 2, 'amazon.com', 'Quarantined', 149),
(12, 2, 'yahoo.com', 'Quarantined', 967),
(4, 3, 'amazon.com', 'Quarantined', 377),
(10, 3, 'yahoo.com', 'Quarantined', 721),
(11, 1, 'yahoo.com', 'Deleted', 551),
(20, 1, 'amazon.com', 'Safe', 407),
(19, 1, 'amazon.com', 'Deleted', 665);
SELECT q.domain_name AS domain_name,t.threat_type AS threat_type,
COUNT(*)AS total_occurrences,SUM(q.users_affected)AS total_users_affected
FROM threat_types t JOIN quarantine_urls q
ON t.id=q.threat_id 
GROUP BY t.threat_type ORDER BY total_users_affected DESC;

-- 46
DROP TABLE IF EXISTS clients46;
CREATE TABLE clients46 (
    id INT PRIMARY KEY,
    mac_address CHAR(17) -- better suited for MAC addresses
);

CREATE TABLE streams46 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    client_id INT NOT NULL,
    title VARCHAR(255) NOT NULL,
    quality ENUM('240p','360p','480p','720p','1080p','1440p','2160p'),
    traffic INT,
    FOREIGN KEY (client_id) REFERENCES clients46(id)
);



INSERT INTO clients46 (id, mac_address) VALUES
(1, '2F-80-8E-F2-0E-4C'),
(2, 'A1-F7-D4-48-B9-E6'),
(3, '9F-72-DB-7C-73-FC');

INSERT INTO streams46 (client_id, title, quality, traffic) VALUES
(1, 'Monte Carlo', '360p', 71928308),
(1, 'Separation, The (Sparation, La)', '480p', 35221785),
(1, 'Felidae', '480p', 54617023),
(1, 'Dirty Dancing', '1440p', 56419563),
(1, 'Ragtime', '1440p', 12404457),
(1, 'Oscar', '1440p', 49717246),
(1, 'Barb Wire', '2160p', 83761463),
(1, 'Jason and the Argonauts', '2160p', 27364051),
(2, 'Carry on Cruising', '240p', 33226462),
(2, 'Best of the Best', '240p', 62793858),
(2, 'Ecstasy (xtasis)', '240p', 73079415),
(2, 'Go Go Tales', '480p', 48836837),
(2, 'Nights and Weekends', '1440p', 32708277),
(3, 'Coneheads', '480p', 92308213),
(3, 'Silences of the Palace, The (Saimt el Qusur)', '480p', 52917945),
(3, 'Good Pick', '720p', 71890218),
(3, 'Wuthering Heights', '720p', 19813053),
(3, 'Big Kahuna, The', '1080p', 28786846),
(3, 'Work of Director Michel Gondry, The', '2160p', 18789351),
(3, 'My Best Friends', '2160p', 44347338);

SELECT c.mac_address AS mac_address,
COUNT(s.client_id) AS streams_total,SUM(s.traffic)AS total_traffic
FROM clients c JOIN streams s ON c.id=s.client_id
WHERE s.quality IN('720p','1080p','1440p','2160p') 
GROUP BY mac_address
ORDER BY total_traffic DESC;

-- 47
CREATE TABLE networks47 (
  id   INT PRIMARY KEY,
  cidr VARCHAR(255)  -- IP Address v4 CIDR
);

CREATE TABLE instances47 (
  network_id    INT,           -- FK to networks.id
  cpu_usage     VARCHAR(255),  -- CPU usage as shown in sample (e.g., '20%')
  memory_usage  VARCHAR(255),  -- Memory usage (e.g., '74%')
  network_usage VARCHAR(255),  -- Network usage (e.g., '79%')
  CONSTRAINT fk_instances_network
    FOREIGN KEY (network_id) REFERENCES networks47(id)
);

INSERT INTO networks47 (id, cidr) VALUES
(1, '24.77.36.156/9'),
(2, '74.213.138.70/7'),
(3, '167.244.163.58/29');
INSERT INTO instances47 (network_id, cpu_usage, memory_usage, network_usage) VALUES
(1, '20%', '74%', '74%'),
(3, '26%', '9%',  '99%'),
(3, '2%',  '21%', '97%'),
(1, '51%', '19%', '89%'),
  (2, '2%',  '27%', '79%'),
  (3, '92%', '35%', '41%'),
  (2, '27%', '5%',  '44%'),
  (3, '67%', '47%', '79%'),
  (1, '14%', '28%', '43%'),
  (3, '47%', '0%',  '53%'),
  (1, '38%', '3%',  '46%'),
  (2, '71%', '51%', '6%'),
  (3, '77%', '74%', '53%'),
  (3, '31%', '48%', '80%'),
  (2, '31%', '42%', '24%'),
  (1, '77%', '65%', '46%'),
  (2, '51%', '94%', '41%'),
  (3, '8%',  '3%',  '57%'),
  (1, '1%',  '56%', '62%'),
  (2, '15%', '66%', '65%');

-- 48
CREATE TABLE tasks48 (
    id INT PRIMARY KEY,
    hash VARCHAR(255)
);

CREATE TABLE processes48 (
    task_id INT,
    start_dt VARCHAR(19),
    end_dt VARCHAR(19),
    FOREIGN KEY (task_id) REFERENCES tasks48(id)
);
INSERT INTO tasks48 (id, hash) VALUES
(1, '208f95e0fcff7926f17ade3cebf33ad9'),
(2, '0f4a49ffead2f18a7f25425c1260fc74'),
(3, 'dbcf54e94935c32e01ec09a5db731912');

INSERT INTO processes48 (task_id, start_dt, end_dt) VALUES
(1, '2023-04-20 02:01:16', '2023-04-20 02:11:35'),
(1, '2023-04-09 15:11:10', '2023-04-09 15:26:43'),
(1, '2023-04-07 23:41:49', '2023-04-08 00:34:10'),
(2, '2023-04-07 23:05:47', '2023-04-08 00:00:05'),
(2, '2023-04-19 18:39:33', '2023-04-19 18:54:57'),
(2, '2023-04-28 13:17:11', '2023-04-28 13:24:37'),
(2, '2023-04-16 00:13:06', '2023-04-16 01:02:39'),
(2, '2023-04-16 15:02:26', '2023-04-16 15:58:14'),
(2, '2023-04-27 02:23:07', '2023-04-27 02:59:13'),
(2, '2023-04-10 23:33:47', '2023-04-11 00:09:35'),
(2, '2023-04-16 17:29:51', '2023-04-16 18:10:22'),
(2, '2023-04-23 12:16:01', '2023-04-23 12:48:07'),
(3, '2023-04-01 02:25:12', '2023-04-01 02:49:26'),
(3, '2023-04-04 03:02:43', '2023-04-04 03:42:03'),
(3, '2023-04-10 22:42:26', '2023-04-10 23:14:42'),
(3, '2023-04-09 17:46:12', '2023-04-09 18:10:19'),
(3, '2023-04-25 15:09:36', '2023-04-25 15:19:54'),
(3, '2023-04-19 14:39:52', '2023-04-19 15:21:23'),
(3, '2023-04-12 04:22:29', '2023-04-12 04:25:10'),
(3, '2023-04-25 07:40:26', '2023-04-25 08:01:30');

-- 49
CREATE TABLE devices49 (
    id INT PRIMARY KEY,
    score INT
);

INSERT INTO devices49 (id, score) VALUES
(1, 20),
(2, 50),
(3, 50),
(4, 68),
(5, 95);


-- 50
CREATE TABLE accounts50 (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100)
);
CREATE TABLE tariffs50 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name ENUM('A','B','C','D','E'),
    cost DECIMAL(10,2)
);


CREATE TABLE readings50 (
    account_id INT,
    tariff_id INT,
    amount SMALLINT,
    FOREIGN KEY (account_id) REFERENCES accounts50(id),
    FOREIGN KEY (tariff_id) REFERENCES tariffs50(id)
);

INSERT INTO accounts (id, username, email) VALUES
(1, 'hshillabeare0', 'rcalkin@sourceforge.net'),
(2, 'sdandy1', 'agaule1@businessweek.com'),
(3, 'sgreiswood2', 'toppy2@lulu.com');

INSERT INTO tariffs (id, name, cost) VALUES
(1, 'A', 0.010),
(2, 'B', 0.020),
(3, 'C', 0.050),
(4, 'D', 0.075),
(5, 'E', 0.100);

INSERT INTO readings (account_id, tariff_id, amount) VALUES
(1, 2, 54),
(1, 3, 19),
(1, 3, 37),
(1, 3, 89),
(1, 3, 119),
(2, 1, 12),
(2, 1, 44),
(2, 1, 81),
(2, 2, 60),
(2, 2, 164),
(2, 2, 199),
(2, 3, 79),
(2, 5, 186),
(3, 1, 31),
(3, 1, 59),
(3, 1, 77),
(3, 1, 95),
(3, 1, 110),
(3, 1, 125),
(3, 2, 31);

SELECT a.username AS username,a.email AS email,
MAX(t.cost)AS highest_traffic,SUM(r.amount)AS consumption,SUM(t.cost)AS total_cost
FROM readings50 r JOIN traffis50 t ON r.account_id=t.id
JOIN accounts50 a ON r.traffic_id=a.id GROUP BY username 
HAVING SUM(r.amount)=SUM(t.cost)
ORDER BY username ASC;
-- 51
 CREATE TABLE cities51 (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE banners51 (
    city_id INT,
    width INT,
    height INT,
    FOREIGN KEY (city_id) REFERENCES cities51(id)
);

INSERT INTO cities51 (id, name) VALUES
(1, 'Kayu Agung'),
(2, 'Yangkou'),
(3, 'Marseille');

INSERT INTO banners51 (city_id, width, height) VALUES
(3, 6, 20),
(1, 20, 14),
(1, 6, 17),
(1, 15, 6),
(2, 16, 8),
(2, 6, 7),
(3, 6, 9),
(1, 20, 16),
(3, 19, 14),
(2, 9, 17);

INSERT INTO banners51 (city_id, width, height) VALUES
(2, 8, 12),
(1, 12, 16),
(3, 15, 14),
(3, 11, 7),
(3, 6, 14),
(2, 12, 7),
(3, 7, 20),
(1, 13, 6),
(3, 10, 13),
(2, 19, 15);
SELECT a.username AS username,a.email AS email,
COUNT(*)AS items,SUM(i.weight)AS total_weight
FROM accounts_items ai JOIN accounts a ON ai.account_id=a.id
JOIN items i ON ai.item_id=i.id GROUP BY a.username
ORDER BY total_weight DESC
,username ASC;

-- 52
SELECT c.name AS city,COUNT(*) AS banner,MIN()AS min_area,
AVG()AS avg_area,MAX()AS max_area,SUM()AS total_area
FROM cities c JOIN banners b ON c.id=b.city_id
ORDER BY city ASC;

-- 53
CREATE TABLE lots53 (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE offers53 (
    lot_id INT,
    amount DECIMAL(6,2),
    FOREIGN KEY (lot_id) REFERENCES lots53(id)
);
INSERT INTO lots53 (id, name) VALUES
(1, 'Merremia quinquefolia (L.) Hallier f.'),
(2, 'Plantago maritima L.'),
(3, 'Hohenbergia antillana Mez'),
(4, 'Penstemon eriantherus Pursh var. argillosus M.E. Jones');

INSERT INTO offers53 (lot_id, amount) VALUES
(1, 510.51), (2, 703.80), (2, 181.80), (1, 38.06), (2, 368.78),
(3, 91.40), (2, 413.80), (3, 157.99), (3, 885.82), (2, 863.99),
(1, 307.61), (2, 120.39), (1, 771.96), (2, 801.42), (3, 871.59),
(1, 541.61), (3, 477.62), (2, 303.29), (2, 612.83), (3, 464.98);


SELECT AS name,COUNT(*)AS offers,ROUND(MIN(o.amount),2)AS min_offer,
ROUND(AVG(o.amount),2)AS avg_offer,ROUND(MAX(o.amount),2)AS max_offer
FROM lots l JOIN offers o ON l.id=o.lot_id
GROUP BY name
ORDER BY offers DESC;

-- 54
CREATE TABLE accounts54 (
    id INT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    iban VARCHAR(34)
);

CREATE TABLE declarations54 (
    account_id INT,
    quarter ENUM('Q1','Q2','Q3','Q4'),
    income DECIMAL(10,2),
    FOREIGN KEY (account_id) REFERENCES accounts54(id)
);

INSERT INTO accounts54 (id, first_name, last_name, iban) VALUES
(1, 'Alex', 'Cantua', 'IL29 9590 1551 0560 0553 712'),
(2, 'Chris', 'Lashmore', 'AZ54 CNUI 01DR XEXZ ASKY QM4W F8JI'),
(3, 'Taylor', 'Blum', 'HR20 2041 7741 5014 9873 9'),
(4, 'Robin', 'Neachell', 'NL87 PPCD 0429 1849 92'),
(5, 'Drew', 'Barbier', 'FR72 7843 3990 42WM QC8P GVNV F78');

INSERT INTO declarations54 (account_id, quarter, income) VALUES
(1, 'Q1', 49235.67), (1, 'Q2', 46653.11), (1, 'Q3', 63739.99), (1, 'Q4', 43222.54),
(2, 'Q1', 69743.50), (2, 'Q2', 29641.01), (2, 'Q3', 97725.49), (2, 'Q4', 91481.98),
(3, 'Q1', 68402.43), (3, 'Q2', 12660.12), (3, 'Q3', 59601.65), (3, 'Q4', 54701.74),
(4, 'Q1', 55220.27), (4, 'Q2', 87752.41), (4, 'Q3', 44447.06), (4, 'Q4', 45876.26),
(5, 'Q1', 42511.74), (5, 'Q2', 22022.78), (5, 'Q3', 88396.81), (5, 'Q4', 67252.54);


SELECT CONCAT(a.first_name,a.last_name) AS full_name ,
a.iban AS iban , SUM(d.income)AS income,'10%'AS rate,
SUM(d.income*0.10)AS tax
FROM accounts a JOIN declarations d
ON a.id=d.account_id 
GROUP BY a.iban ORDER BY full_name ASC;
-- 55
CREATE TABLE profiles55 (
    id INT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE relations55 (
    profile_id INT,
    related_to VARCHAR(255),
    is_approved BOOLEAN,
    FOREIGN KEY (profile_id) REFERENCES profiles55(id)
);
INSERT INTO profiles55 (id, first_name, last_name, email) VALUES
(1, 'Shayne', 'Shilito', 'sshilito0@ftc.gov'),
(2, 'Shell', 'Shade', 'sshade1@paginegialle.it'),
(3, 'Nobie', 'Splain', 'nsplain2@npr.org');

INSERT INTO relations55 (profile_id, related_to, is_approved) VALUES
(1, 'cbasinigazzii', TRUE), (1, 'ldevered', TRUE), (1, 'edeniskeb', TRUE),
(1, 'cstirland4', TRUE), (1, 'ngooddiea', TRUE), (1, 'alockney7', TRUE),
(1, 'jsorrillj', FALSE), (1, 'bnodin3', FALSE), (1, 'dwall2', FALSE),
(1, 'folivas1', FALSE), (2, 'ksharland6', FALSE), (2, 'pbarosch8', FALSE),
(2, 'smacieja9', FALSE), (2, 'bbrasonf', FALSE), (2, 'dabrahartg', FALSE),
(3, 'gaymer5', TRUE), (3, 'rwoolcockse', TRUE), (3, 'egilyott0', TRUE),
(3, 'agillionc', FALSE), (3, 'fgribbinh', FALSE);
SELECT CONCAT(p.first_name,p.last_name) AS full_name ,
p.email AS email , AS total_relations,
SUM(
CASE 
	WHEN r.is_approved IS TRUE THEN 1
	ELSE 0
END
)AS approved_relations,
SUM(
CASE 
	WHEN r.is_approved IS FALSE THEN 1
	ELSE 0
END
)
AS pending_relations
FROM accounts a JOIN declarations d
ON a.id=d.account_id 
GROUP BY a.iban ORDER BY full_name ASC;

-- 56
 CREATE TABLE accounts56 (
    id INT PRIMARY KEY,
    iban VARCHAR(255)
);
DROP TABLE IF EXISTS transactions56;
CREATE TABLE transactions56 (
    account_id INT,
    dt TIMESTAMP,
    amount DECIMAL(5,2),
    FOREIGN KEY (account_id) REFERENCES accounts56(id)
);

INSERT INTO accounts56 (id, iban) VALUES
(1, 'SE48 2961 2087 8112 2835 6438'),
(2, 'BE89 2286 5514 4847'),
(3, 'MU84 HRGV 2047 2584 5774 3195 856J PZ');

INSERT INTO transactions56 (account_id, dt, amount) VALUES
(2, '2022-09-25 19:24:50', 75.06),
(2, '2022-09-24 03:09:17', 41.10),
(1, '2022-09-19 04:13:17', 65.85),
(3, '2022-09-30 07:18:29', 44.57),
(1, '2022-09-26 01:51:44', 98.93),
(1, '2022-08-28 02:51:04', 60.42),
(1, '2022-08-25 23:25:54', 45.34),
(2, '2022-09-09 11:00:48', 11.05),
(3, '2022-08-25 19:37:02', 53.61),
(2, '2022-09-23 09:44:05', 89.18),
(1, '2022-08-28 19:48:40', 47.60),
(3, '2022-09-12 10:28:10', 96.40),
(3, '2022-10-03 16:49:51', 45.41),
(2, '2022-09-05 16:20:41', 46.78),
(3, '2022-10-03 04:51:29', 50.81),
(1, '2022-09-10 17:31:44', 78.72),
(2, '2022-08-31 21:59:56', 61.09),
(2, '2022-09-14 12:52:13', 20.36),
(1, '2022-09-28 11:05:21', 70.52),
(3, '2022-09-30 09:21:12', 48.00);

SELECT AS iban,COUNT(*)AS transactions,SUM(t.amount)AS total
FROM accounts a JOIN transactions t
ON a.id=t.account_id WHERE MONTHNAME(t.dt)='september'
GROUP BY a.iban ORDER BY total DESC;

-- 57
CREATE TABLE clients57 (
    id SMALLINT PRIMARY KEY,
    mac VARCHAR(17),
    tariff DECIMAL(6,5)
);

CREATE TABLE traffic57 (
    client_id SMALLINT,
    dt VARCHAR(19),
    amount INT,
    FOREIGN KEY (client_id) REFERENCES clients57(id)
);
INSERT INTO clients57 (id, mac, tariff) VALUES
(1, 'A2-53-FC-0C-3E-B4', 0.00007),
(2, 'DC-80-42-E9-AE-FC', 0.00003),
(3, '3F-9B-A9-2A-B1-7B', 0.00007),
(4, 'D4-6F-E4-AF-47-D5', 0.00004),
(5, 'B9-65-FC-8E-F0-15', 0.00007);

INSERT INTO traffic57 (client_id, dt, amount) VALUES
(1, '2022-05-22', 9127),
(1, '2022-06-07', 62203),
(1, '2022-06-10', 88227),
(2, '2022-05-31', 99874),
(2, '2022-06-01', 78400),
(2, '2022-06-03', 61106),
(2, '2022-06-12', 20963),
(2, '2022-06-29', 98304),
(2, '2022-07-04', 6626),
(3, '2022-05-22', 8386),
(3, '2022-06-08', 22959),
(3, '2022-07-05', 52096),
(3, '2022-07-14', 70777),
(4, '2022-05-22', 93743),
(5, '2022-05-16', 84660),
(5, '2022-05-28', 63267),
(5, '2022-06-10', 80681),
(5, '2022-06-21', 55460),
(5, '2022-07-04', 91365),
(5, '2022-07-09', 23296);

SELECT c.mac AS mac,COUNT(*)AS traffic,SUM(t.amount)AS cost 
FROM clients c JOIN traffic t ON c.id=t.client_id
WHERE t.dt LIKE '2022-05%'
GROUP BY mac 
ORDER BY cost DESC;

-- 58
CREATE TABLE companies58 (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255),
    address VARCHAR(255),
    phone VARCHAR(255)
);

CREATE TABLE categories58 (
    company_id SMALLINT,
    name VARCHAR(255),
    review_rating SMALLINT,
    FOREIGN KEY (company_id) REFERENCES companies58(id)
);

INSERT INTO companies58 (id, name, address, phone) VALUES
(1, 'Casper, Oberbrunner and Williamson', '53 Di Loreto Hill', '+420 (569) 566-3689'),
(2, 'Tromp, Kozey and Abbott', '84 Mcguire Plaza', '+62 (145) 722-2330'),
(3, 'Gerlach, Hayes and Stamm', '80 Service Point', '+86 (731) 234-4119'),
(4, 'Wol -Fadel', '06 Fair Oaks Trail', '+7 (894) 233-0976'),
(5, 'Kihn-Cronin', '483 Nobel Road', '+1 (396) 693-1661');

INSERT INTO categories58 (company_id, name, review_rating) VALUES
(1, 'HVAC', 2),
(2, 'HVAC', 2), (2, 'Retaining Wall and Brick Pavers', 1), (2, 'Rebar & Wire Mesh Install', 2),
(3, 'Prefabricated Aluminum Metal Canopies', 2), (3, 'Prefabricated Aluminum Metal Canopies', 0),
(3, 'RF Shielding', 2), (3, 'Overhead Doors', 0), (3, 'Rebar & Wire Mesh Install', 5),
(3, 'Termite Control', 0),
(4, 'Sitework & Site Utilities', 0), (4, 'Electrical and Fire Alarm', 2),
(4, 'Masonry', 2), (4, 'Temp Fencing, Decorative Fencing and Gates', 0),
(4, 'Elevator', 1), (4, 'Drywall & Acoustical (FED)', 5),
(5, 'Asphalt Paving', 0), (5, 'Glass & Glazing', 1),
(5, 'Framing (Steel)', 3), (5, 'Structural & Misc Steel Erection', 1);
SELECT c.name AS name,c.address AS address,c.phone AS phone
,AS overall_review_rating
FROM companies c JOIN categories t ON c.id=t.company_id

GROUP BY 
ORDER BY cost DESC; 

-- 59
CREATE TABLE accounts59 (
    id SMALLINT PRIMARY KEY,
    username VARCHAR(255),
    is_active SMALLINT
);

CREATE TABLE domains59 (
    account_id SMALLINT,
    name VARCHAR(255),
    expiration_date VARCHAR(19),
    FOREIGN KEY (account_id) REFERENCES accounts59(id)
);

INSERT INTO accounts59 (id, username, is_active) VALUES
(1, 'obeedie0', 0),
(2, 'stopham1', 1),
(3, 'ndolder2', 1),
(4, 'jyanshinov3', 1),
(5, 'ewilinger4', 0);


INSERT INTO domains59 (account_id, name, expiration_date) VALUES
(1, 'imgur.com', '2022-05-14'),
(1, 'domainmarket.com', '2022-07-02'),
(1, 'comsenz.com', '2022-07-28'),
(1, 'gizmodo.com', '2022-08-09'),
(1, 'toplist.cz', '2022-08-15'),
(1, 'scientificamerican.com', '2022-09-03'),
(1, 'examiner.com', '2022-12-18'),
(1, 'photobucket.com', '2023-01-22'),
(2, 'merriam-webster.com', '2022-02-20'),
(2, 'tripod.com', '2022-08-08'),
(3, 'ca.gov', '2022-04-24'),
(3, 'ehow.com', '2022-06-28'),
(3, 'purevolume.com', '2022-07-01'),
(3, 'squidoo.com', '2022-10-27'),
(3, 'eepurl.com', '2022-12-21'),
(4, 'digg.com', '2022-05-14'),
(4, 'jugem.jp', '2022-08-05'),
(4, 'artisteer.com', '2022-10-21'),
(5, 'behance.net', '2022-03-24'),
(5, 'cnn.com', '2022-05-11');
SELECT AS username,AS domains,AS nearest_expiration
FROM accounts a JOIN domains d ON a.id=d.account_id
WHERE d.expiration_date LIKE '2022-07-15'
GROUP BY a.id,a.username
ORDER BY username ASC;

-- 60
CREATE TABLE campaigns60 (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE events60 (
    campaign_id SMALLINT,
    dt VARCHAR(19),
    value DECIMAL(6,5),
    FOREIGN KEY (campaign_id) REFERENCES campaigns60(id)
);


INSERT INTO campaigns60 (id, name) VALUES
(1, '11-080 - Registration Equipment'),
(2, '12-700 - Systems Furniture'),
(3, '9-900 - Paints and Coatings');

INSERT INTO events60 (campaign_id, dt, value) VALUES
(1, '2022-07-14 13:11:38', 0.59275), (1, '2022-07-14 14:55:43', 0.12928),
(1, '2022-07-14 18:16:11', 0.82350), (1, '2022-07-15 01:19:44', 0.97144),
(1, '2022-07-15 22:52:02', 0.60728), (1, '2022-07-16 08:55:38', 0.71158),
(1, '2022-07-16 10:22:44', 0.29627),
(2, '2022-07-14 02:36:31', 0.42323), (2, '2022-07-14 04:45:32', 0.91077),
(2, '2022-07-14 07:24:11', 0.35956), (2, '2022-07-15 06:43:08', 0.16662),
(2, '2022-07-16 08:21:27', 0.02559), (2, '2022-07-16 11:59:41', 0.34606),
(2, '2022-07-16 23:26:12', 0.62697),
(3, '2022-07-14 00:21:56', 0.97297), (3, '2022-07-14 10:22:11', 0.93894),
(3, '2022-07-14 12:29:59', 0.44633), (3, '2022-07-15 01:17:41', 0.37531),
(3, '2022-07-15 14:20:48', 0.24872), (3, '2022-07-16 23:02:51', 0.80594);

SELECT AS campaign, COUNT(*)AS events,AVG(e.value)AS average_value
FROM campaign c JOIN events e ON c.id=e.campaign_id
WHERE DATE(e.dt)='2022-07-15' 
GROUP BY c.id,c.name HAVING AVG(e.value)>=0.7 
ORDER BY average_value DESC;

-- 61
CREATE TABLE profiles (
    id SMALLINT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE deals (
    profile_id SMALLINT,
    dt VARCHAR(19),
    amount DECIMAL(5,2),
    FOREIGN KEY (profile_id) REFERENCES profiles(id)
);

INSERT INTO profiles (id, first_name, last_name, email) VALUES
(1, 'Wallis', 'Treadway', 'wtreadway0@senate.gov'),
(2, 'Franklin', 'Blackston', 'fblackston1@parallels.com'),
(3, 'Honoria', 'Constant', 'hconstant2@umich.edu'),
(4, 'Bertine', 'Hillaby', 'bhillaby3@artisteer.com'),
(5, 'Constance', 'Knutsen', 'cknutsen4@google.ca');

INSERT INTO deals (profile_id, dt, amount) VALUES
(4, '2022-06-04', 22.31), (4, '2022-06-04', 36.33), (5, '2022-06-04', 21.41),
(1, '2022-06-07', 92.84), (4, '2022-06-08', 24.41), (3, '2022-06-13', 61.55),
(4, '2022-06-16', 77.70), (5, '2022-06-18', 58.79), (4, '2022-06-20', 43.61),
(3, '2022-06-22', 10.41), (1, '2022-06-23', 6.59), (1, '2022-06-30', 43.07);

SELECT SUM(d.amount)AS total FROM profiles p 
JOIN deals d ON p.id=d.profile_id
WHERE d.dt LIKE '2022-06' GROUP BY p.id ORDER BY total DESC LIMIT 3;

-- 62
CREATE TABLE profiles (
    id SMALLINT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255),
    is_verified SMALLINT
);

CREATE TABLE stats (
    profile_id SMALLINT,
    job_success_score SMALLINT,
    FOREIGN KEY (profile_id) REFERENCES profiles(id)
);

INSERT INTO profiles (id, first_name, last_name, email, is_verified) VALUES
(1, 'Junia', 'Sehorsch', 'jsehorsch0@oracle.com', 0),
(4, 'Kendell', 'Sylvester', 'ksylvester3@canalblog.com', 1),
(5, 'Koralle', 'Ragsdale', 'kragsdale4@buzzfeed.com', 1),
(10, 'Sophie', 'Messenger', 'smessenger9@myspace.com', 1),
(12, 'Kayle', 'Jesteco', 'kjestecob@ocn.ne.jp', 1),
(13, 'Munroe', 'Chevolleau', 'mchevolleauc@yandex.ru', 1),
(14, 'Etheline', 'Choake', 'echoaked@hao123.com', 1),
(18, 'Margo', 'Finnemore', 'mfinnemoreh@discovery.com', 1),
(20, 'Demetris', 'Arnet', 'darnetj@livejournal.com', 1),
(17, 'Jori', 'MacFaell', 'jmacfaellg@va.gov', 1),
(7, 'Harold', 'Molloy', 'hmolloy6@ycombinator.com', 1);

INSERT INTO stats (profile_id, job_success_score) VALUES
(4, 100), (5, 95), (10, 95), (12, 95), (13, 95), (14, 95),
(18, 95), (20, 90), (17, 90), (7, 90);

SELECT p.first_name AS first_name,p.last_name AS last_name,
p.email AS email,
SUM(s.job_success_score)AS job_success_score
FROM profiles p JOIN stats s ON p.id=s.profile_id
GROUP BY p.id HAVING SUM(s.job_success_score)>90
ORDER BY job_success_score DESC,first_name ASC,last_name ASC;

-- 63
CREATE TABLE configurations (
    id VARCHAR(64) PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE deployments (
    configuration_id VARCHAR(64),
    dt VARCHAR(19),
    FOREIGN KEY (configuration_id) REFERENCES configurations(id)
);

INSERT INTO configurations (id, name) VALUES
('1vcpu_512mb_10gb_500gb', '1 CPU / 512 MB RAM / 10 GB SSD Disk / 500 GB transfer'),
('1vcpu_1gb_25gb_1tb', '1 CPU / 1 GB RAM / 25 GB SSD Disk / 1000 GB transfer'),
('1vcpu_2gb_50gb_2tb', '1 CPU / 2 GB RAM / 50 GB SSD Disk / 2 TB transfer'),
('2vcpu_2gb_60gb_3tb', '2 CPUs / 2 GB RAM / 60 GB SSD Disk / 3 TB transfer'),
('2vcpu_4gb_80gb_4tb', '2 CPUs / 4 GB RAM / 80 GB SSD Disk / 4 TB transfer'),
('4vcpu_8gb_160gb_5tb', '4 CPUs / 8 GB RAM / 160 GB SSD Disk / 5 TB transfer'),
('8vcpu_16gb_320gb_6tb', '8 CPUs / 16 GB RAM / 320 GB SSD Disk / 6 TB transfer');

INSERT INTO deployments (configuration_id, dt) VALUES
('2vcpu_2gb_60gb_3tb', '2021-01-24'),
('4vcpu_8gb_160gb_5tb', '2021-01-25'),
('2vcpu_2gb_60gb_3tb', '2021-02-08'),
('1vcpu_512mb_10gb_500gb', '2021-03-25'),
('8vcpu_16gb_320gb_6tb', '2021-03-26'),
('2vcpu_2gb_60gb_3tb', '2021-05-24'),
('1vcpu_512mb_10gb_500gb', '2021-05-31'),
('1vcpu_2gb_50gb_2tb', '2021-08-25'),
('2vcpu_4gb_80gb_4tb', '2021-09-05'),
('2vcpu_4gb_80gb_4tb', '2021-09-23'),
('2vcpu_4gb_80gb_4tb', '2021-09-28'),
('2vcpu_2gb_60gb_3tb', '2021-10-14'),
('1vcpu_2gb_50gb_2tb', '2021-11-01'),
('1vcpu_512mb_10gb_500gb', '2021-11-26'),
('1vcpu_1gb_25gb_1tb', '2021-12-27');

SELECT c.name AS configuration,COUNT(*)AS deployments
FROM configurations c JOIN deployments d
ON c.id=d.configuration_id
WHERE d.dt LIKE '2021%' GROUP BY c.id ORDER BY deployments DESC;

-- 64
CREATE TABLE events (
    dt VARCHAR(19),
    type VARCHAR(64)
);

INSERT INTO events (dt, type) VALUES
('2022-05-27 16:12:50', 'buy'),
('2022-05-20 09:09:07', 'buy'),
('2022-05-22 09:06:37', 'buy'),
('2022-05-31 07:49:36', 'buy'),
('2022-05-14 22:29:10', 'buy'),
('2022-05-13 15:00:54', 'sell'),
('2022-05-24 15:40:54', 'sell'),
('2022-05-13 01:20:05', 'sell'),
('2022-05-16 07:07:44', 'sell'),
('2022-05-01 16:57:00', 'sell');

SELECT COUNT(*) AS purchases  FROM events 
WHERE e.dt LIKE '2022-05%' AND type='buy'
GROUP BY type;
-- 65
 CREATE TABLE companies (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE campaigns (
    company_id SMALLINT,
    expenses DECIMAL(7,2),
    revenue DECIMAL(7,2),
    FOREIGN KEY (company_id) REFERENCES companies(id)
);

INSERT INTO companies (id, name) VALUES
(1, 'Lion Biotechnologies, Inc.'),
(2, 'Boston Private Financial Holdings, Inc.'),
(3, 'Universal Corporation'),
(4, 'Arbutus Biopharma Corporation'),
(5, 'Royal Bank Of Canada'),
(6, 'Penn West Petroleum Ltd'),
(7, 'Public Storage'),
(8, 'Halcon Resources Corporation'),
(9, 'TTM Technologies, Inc.'),
(10, 'Atwood Oceanics, Inc.'),
(11, 'ACADIA Pharmaceuticals Inc.'),
(12, 'Central European Media Enterprises Ltd.'),
(13, 'Oxbridge Re Holdings Limited'),
(14, 'Western Refining Logistics, LP'),
(15, 'Vaalco Energy Inc'),
(16, 'Xilinx, Inc.'),
(17, 'Liberty Global plc'),
(18, 'Honda Motor Company, Ltd.'),
(19, 'Great Plains Energy Inc'),
(20, 'Assurant, Inc.');

INSERT INTO campaigns (company_id, expenses, revenue) VALUES
(1, 7390.24, 8652.18), (2, 5774.65, 7955.47), (3, 2154.71, 5920.23),
(4, 9366.49, 3397.85), (5, 2765.18, 9158.63), (6, 7908.41, 5018.85),
(7, 2251.44, 6654.52), (8, 3383.14, 9354.79), (9, 8287.96, 9522.53),
(10, 4356.62, 4658.52), (11, 9272.86, 9161.77), (12, 4996.18, 5903.57),
(13, 8354.75, 2259.26), (14, 6402.90, 8146.16), (15, 1692.05, 686.71),
(16, 5988.48, 9089.41), (17, 6192.33, 7580.19), (18, 3016.37, 7761.25),
(19, 9838.05, 1293.09), (20, 4386.52, 9513.73);


SELECT AS company_name,SUM(c.revenue)-SUM(c.expenses)AS profit
FROM companies c JOIN campaigns t
ON c.id=t.company_id GROUP BY c.id,c.name 
HAVING SUM(c.revenue)-SUM(c.expenses)>0
ORDER BY profit DESC;

-- 66
CREATE TABLE clients (
    mac VARCHAR(64),
    upstream_rate INT,
    downstream_rate INT,
    downtime_rate VARCHAR(64)
);
INSERT INTO clients (mac, upstream_rate, downstream_rate, downtime_rate) VALUES
('78-C1-E5-20-D5-61', 925526, 5195, 'never'),
('78-E2-20-71-9C-30', 582152, 375829, 'never'),
('0D-09-F7-77-03-E5', 359529, 710743, 'never'),
('56-18-67-55-58-EA', 78626, 562544, 'once'),
('C9-73-EC-1C-4C-B7', 574927, 669655, 'yearly'),
('02-35-3F-7B-CC-76', 430072, 296196, 'seldom'),
('3D-95-33-8A-65-F9', 894176, 489401, 'monthly'),
('8D-33-3F-0E-04-D5', 897666, 297063, 'weekly'),
('0E-0A-63-B9-79-3E', 69133, 984354, 'seldom'),
('15-D3-2A-DD-02-A4', 19203, 995983, 'seldom'),
('8D-42-B1-97-AB-87', 476648, 177677, 'monthly'),
('6B-64-60-47-16-D3', 700056, 374321, 'monthly'),
('FD-61-81-00-BF-EC', 216401, 498229, 'yearly'),
('95-46-C6-C7-6F-E0', 236331, 341013, 'monthly'),
('11-0E-62-32-62-5E', 694746, 451525, 'daily'),
('D6-5B-72-D5-FF-4F', 2931, 992852, 'monthly'),
('66-BF-AD-F2-E5-45', 861075, 44216, 'monthly'),
('E5-C9-C6-74-2E-A8', 639487, 968494, 'daily'),
('18-56-3A-93-8E-9F', 494945, 259910, 'weekly'),
('51-EB-D7-22-45-99', 219419, 326479, 'often');


SELECT AS mac,AS upstream_rate,AS downstream_rate,AS downtime_rate
FROM clients c JOIN 

-- 67
CREATE TABLE owners (
    id SMALLINT PRIMARY KEY,
    full_name VARCHAR(255),
    email_address VARCHAR(255),
    on_vacation SMALLINT
);

CREATE TABLE events (
    owner_id SMALLINT,
    dt VARCHAR(19),
    title VARCHAR(255)
);


INSERT INTO owners (id, full_name, email_address, on_vacation) VALUES
(1, 'Benjamin Sevier', 'bsevier0@discuz.net', 1),
(2, 'Aleksandr Fellows', 'afellows1@instagram.com', 0),
(3, 'Collete Pack', 'cpack2@mit.edu', 0),
(4, 'Lorelle Squibb', 'lsquibb3@huffingtonpost.com', 0),
(5, 'Cointon Welberry', 'cwelberry4@theguardian.com', 1);

INSERT INTO events (owner_id, dt, title) VALUES
(1, '2021-08-13 09:17:41', 'ac consequat metus sapien ut'),
(1, '2021-10-26 16:14:28', 'a libero nam'),
(1, '2022-05-18 16:09:36', 'vestibulum ante ipsum primis in faucibus'),
(1, '2022-06-04 20:37:36', 'nisi volutpat eleifend donec'),
(2, '2021-01-17 23:34:59', 'eu mi nulla ac'),
(2, '2021-01-26 15:59:04', 'nisl venenatis lacinia'),
(2, '2021-04-25 05:04:29', 'arcu adipiscing molestie hendrerit'),
(2, '2022-02-22 10:24:50', 'lectus vestibulum quam sapien varius'),
(2, '2022-05-22 03:04:33', 'pellentesque quisque porta volutpat erat'),
(3, '2021-03-11 08:25:21', 'felis sed interdum venenatis'),
(3, '2022-02-15 07:29:45', 'nulla sed accumsan felis ut'),
(4, '2021-02-28 16:10:55', 'cras mi pede malesuada in imperdiet et'),
(4, '2021-05-20 03:35:11', 'odio in hac habitasse platea dictumst'),
(4, '2021-09-01 19:25:41', 'sagittis nam congue risus semper'),
(4, '2022-02-04 03:11:53', 'luctus cum sociis natoque penatibus et magnis'),
(4, '2022-06-18 08:39:41', 'nec molestie sed justo pellentesque'),
(5, '2021-04-04 13:39:38', 'id massa id nisl venenatis lacinia aenean'),
(5, '2021-10-18 19:04:14', 'adipiscing lorem vitae mattis'),
(5, '2021-10-19 00:56:11', 'vestibulum eget vulputate ut ultrices vel augue'),
(5, '2022-04-26 18:55:04', 'at dolor quis');

-- 68
CREATE TABLE clients (
    id SMALLINT,
    mac VARCHAR(64)
);

CREATE TABLE traffic (
    client_id SMALLINT,
    amount INT
);
-- clients
INSERT INTO clients (id, mac) VALUES
(1, 'E5-A3-AC-8A-20-F9'),
(2, '3B-2F-83-25-A8-81'),
(3, '3A-4E-A6-43-1D-B1'),
(4, 'B7-03-14-91-8F-58'),
(5, '63-1A-FD-9A-AF-6F'),
(6, 'D6-B8-1F-1D-34-04'),
(7, '38-83-E5-F8-C8-DC'),
(8, 'E9-F6-89-7F-8D-34'),
(9, '82-2E-B3-67-04-41'),
(10, 'D1-9D-DE-37-A0-49'),
(11, '8A-46-F4-83-29-13'),
(12, '4C-1F-7B-C7-08-7E'),
(13, '72-57-E6-CA-2C-91'),
(14, '57-F7-E7-E7-45-36'),
(15, '05-8A-05-1D-2D-20'),
(16, '46-06-F1-B9-65-7C'),
(17, '0A-E0-26-9D-2A-27'),
(18, 'F0-86-99-18-36-9B'),
(19, 'DD-81-BF-53-BD-9B'),
(20, '50-53-64-8E-42-BE');

-- traffic
INSERT INTO traffic (client_id, amount) VALUES
(3, 6385047),
(8, 6490817),
(14, 9109219),
(16, 5558512),
(17, 1870152),
(17, 8228920),
(18, 326127),
(18, 5429741),
(18, 4063477),
(19, 7411789),
(20, 5832337),
(20, 1426585),
(23, 1097368),
(23, 6769594),
(23, 802387),
(24, 5959513),
(24, 1300408),
(24, 4631624),
(28, 1629306),
(29, 2814818);

-- 69
CREATE TABLE wallets (
    id SMALLINT PRIMARY KEY,
    address VARCHAR(64)
);

CREATE TABLE transactions (
    wallet_id SMALLINT,
    credit DECIMAL(4,2)
);

INSERT INTO wallets (id, address) VALUES
(1, '0x1ecc4cefde6dfb773352a2dcd8b5f518ccd24'),
(2, '0x0f4487168610dcae7f16b6c000a7ba284bb6703c'),
(3, '0x54c5c516b5c601a3e6fdea18db966186274879e6'),
(4, '0x50c404a6790d44849c3500d95f147d2102db5f77'),
(5, '0x41cc32037fdcc6cbf5cb04ada4e38032a84361ac'),
(6, '0xb38ca2076b2c3a0a1c0796ccab5dc3fcbb645336'),
(7, '0x923c6ee4debce6c37c512eb90e9b26fc9bbfc3e1'),
(8, '0x76cfbf67e1765117a98fd2286bdbcb731b0d29ec'),
(9, '0xf4d43df89365453440dbeb5e53445d3400d218'),
(10, '0x010a4b23f985eda6b397864878cb70bf0b233aa6'),
(11, '0x106c4c852394d5a6580c11ca0060687a798ec78c'),
(12, '0x541da74b9f4b2283524a65156263bcce7429ba61'),
(13, '0x86df8e62ad23a35ee2e64b3b4b128d8a3660116a'),
(14, '0x6b021b12b779aec27009f18bd6ad227685df5474'),
(15, '0x7073e9f48b7211d9940db4c8bad8e31d5d9d0577'),
(16, '0x4672e132b9627a7db76e5af431bb5febc93b1b2f'),
(17, '0x5cbc82a01d3b9df8f50ee09bf5a1c48afe0cb966'),
(18, '0x1c7d30f90081b45004e0d6549534ef8658795672'),
(19, '0xf830618ca9f027ce5f23bf270629a6a418ad355c'),
(20, '0x1a31ac1b923fcc17a42a340091424ea18192a3e7');

INSERT INTO transactions (wallet_id, credit) VALUES
(3, 5.21),
(6, 24.04),
(8, 29.66),
(10, 1.67), (10, 3.32), (10, 4.27),
(14, 3.32), (14, 3.35),
(15, 14.34),
(16, 11.94),
(20, 13.86), (20, 16.54),
(21, 11.64),
(25, 12.86),
(26, 8.77), (26, 12.36),
(27, 29.65), 
(28, 3.00), (28, 13.26), (28, 2.85);

-- 70
CREATE TABLE animals (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(64)
);

CREATE TABLE tracklog (
    animal_id SMALLINT,
    tracked_at VARCHAR(19)
);

INSERT INTO animals (id, name) VALUES
(1, 'Blue and yellow macaw'),
(2, 'Jungle kangaroo'),
(3, 'Stork, woolly-necked'),
(4, 'North American river otter'),
(5, 'Square-lipped rhinoceros'),
(6, 'Black-fronted bulbul'),
(7, 'American beaver'),
(8, 'Capybara'),
(9, 'Black-backed jackal'),
(10, 'Dragon, ornate rock'),
(11, 'Wombat, southern hairy-nosed'),
(12, 'Snake, carpet'),
(13, 'Egyptian cobra'),
(14, 'Green heron'),
(15, 'Indian star tortoise'),
(16, 'Roan antelope'),
(17, 'Rhea, common'),
(18, 'Fairy penguin'),
(19, 'Black-eyed bulbul'),
(20, 'Starling, cape');

INSERT INTO tracklog (animal_id, tracked_at) VALUES
(2, '2021-07-08 12:30:34'),
(5, '2021-09-15 03:00:04'),
(8, '2021-12-14 11:20:50'),
(9, '2021-05-15 14:54:15'),
(11, '2021-07-03 17:04:14'),
(11, '2021-02-23 09:02:11'),
(15, '2021-11-02 22:49:37'),
(15, '2021-07-31 04:51:25'),
(15, '2021-06-06 14:57:23'),
(19, '2021-03-25 02:00:31'),
(19, '2021-04-21 22:02:51'),
(19, '2021-07-17 17:12:35'),
(21, '2021-05-06 03:35:59'),
(22, '2021-09-21 22:49:36'),
(22, '2021-12-04 00:26:29'),
(24, '2021-12-27 08:19:52'),
(26, '2021-05-14 07:30:11'),
(28, '2021-06-08 00:47:00'),
(28, '2021-04-24 00:42:31'),
(30, '2021-05-06 21:03:03');

SELECT AS ,AS ,AS FROM JOIN ON 

-- 71
CREATE TABLE CUSTOMERS (
    id INT,
    customer_name VARCHAR(30)
);

CREATE TABLE ORDERS (
    order_id INT,
    customer_id INT,
    order_type VARCHAR(5),
    order_amount DECIMAL(18,2)
);

INSERT INTO CUSTOMERS (id, customer_name) VALUES
(401, 'Hubert Keesler'),
(402, 'Devin Vert'),
(403, 'Lashawna Bowerman'),
(404, 'Brigid Wellborn'),
(405, 'Josefine Perl');

INSERT INTO ORDERS (order_id, customer_id, order_type, order_amount) VALUES
(4361, 401, 'Sell', 912.77),
(3478, 405, 'Sell', 741.69),
(7292, 405, 'Sell', 436.05),
(5833, 405, 'Sell', 231.30),
(3472, 402, 'Buy', 950.92),
(4472, 401, 'Sell', 367.70),
(2624, 404, 'Buy', 218.15),
(7198, 405, 'Buy', 797.29),
(7660, 403, 'Buy', 131.18),
(5192, 401, 'Buy', 362.44),
(5260, 402, 'Buy', 636.26),
(2726, 403, 'Sell', 138.15),
(6594, 401, 'Buy', 234.51),
(4657, 404, 'Buy', 427.30),
(9744, 402, 'Sell', 623.36);

SELECT c.customer_name AS customer_name,
SUM(o.order_amount)AS total_fees FROM customers71 c JOIN orders71 o 
ON c.id=o.customer_id
ORDER BY customer_name ASC;

-- 72
CREATE TABLE CREDIT_HOLDERS (
    id INT PRIMARY KEY,
    first_name VARCHAR(15),
    last_name VARCHAR(15),
    interest_rate INT
);

CREATE TABLE TRANSACTIONS (
    transaction_id INT PRIMARY KEY,
    credit_holder_id INT,
    amount DECIMAL(18,2),
    FOREIGN KEY (credit_holder_id) REFERENCES CREDIT_HOLDERS(id)
);

INSERT INTO CREDIT_HOLDERS (id, first_name, last_name, interest_rate) VALUES
(101, 'Clemencia', 'Hutsell', 12),
(102, 'Susannah', 'Ismail', 18),
(103, 'Sixta', 'Hagy', 18),
(104, 'Otto', 'Izquierdo', 18),
(105, 'Anita', 'Degroot', 15);


INSERT INTO TRANSACTIONS (transaction_id, credit_holder_id, amount) VALUES
(4361, 101, 65.22),
(3478, 104, 51.85),
(7292, 104, 64.60),
(5833, 105, 72.15),
(3472, 102, 96.28),
(4472, 101, 80.06),
(2624, 101, 85.27),
(7198, 104, 23.73),
(7660, 103, 81.86),
(5192, 101, 69.64),
(5260, 101, 71.72),
(2726, 102, 57.66),
(6594, 103, 23.23),
(4657, 101, 81.68),
(9744, 104, 99.57),
(2054, 103, 51.13),
(7156, 105, 12.78),
(3273, 105, 36.15),
(9756, 101, 45.41),
(9702, 105, 69.75);
SELECT c.customer_name AS full_name,
SUM(o.order_amount)AS dues FROM credit_holders72 c 
JOIN transactions71 t 
ON c.id=t.transaction_id
ORDER BY customer_name ASC;

-- 73
CREATE TABLE accounts (
    id INT PRIMARY KEY,
    account_holder VARCHAR(30),
    amount VARCHAR(10)
);

INSERT INTO accounts (id, account_holder, amount) VALUES
(1, 'Ellis Beane', '$5582.03'),
(2, 'Drew Nolf', '$2470.3'),
(3, 'Jordan Chatmon', '$6211.52'),
(4, 'Robin Hansard', '$8133.31'),
(5, 'Spencer Days', '$5273.81'),
(6, 'Morgan Criss', '$3741.85'),
(7, 'Wesley Waugh', '$7056.14'),
(8, 'Alex Canty', '$2590.45'),
(9, 'Blake Hawbaker', '$3987.27'),
(10, 'Taylor Blackston', '$8351.98');

SELECT  account_holder,
AS interest FROM accounts73 a 
ORDER BY customer_name ASC;
-- 74
CREATE TABLE transactions (
    transaction_id VARCHAR(10) PRIMARY KEY,
    amount DECIMAL(18,2)
);

INSERT INTO transactions (transaction_id, amount) VALUES
('19SEP2187', 785.72),
('19OCT4361', 752.64),
('19APR3478', 197.92),
('19SEP7292', 910.26),
('21MAR5833', 344.70),
('20MAY3472', 939.61),
('20DEC4472', 154.98),
('20DEC2624', 935.44),
('21JUN7198', 309.81),
('19APR7660', 528.10),
('20MAR5192', 995.22),
('19OCT5260', 861.11),
('21JUN2726', 611.94),
('19OCT6594', 478.54),
('19APR4657', 183.20);

SELECT AS year,
AS month FROM transactions74 t 
ON c.id=t.transaction_id
ORDER BY year ASC,month ASC;
-- 75
CREATE TABLE RESULTS (
    id INT,
    first_name VARCHAR(20),
    last_name VARCHAR(20),
    cgpa_first_year FLOAT,
    cgpa_second_year FLOAT,
    cgpa_third_year FLOAT,
    cgpa_fourth_year FLOAT
);

INSERT INTO results (
    id, first_name, last_name, 
    cgpa_first_year, cgpa_second_year, cgpa_third_year, cgpa_fourth_year
) VALUES
(1, 'Pearlene', 'Beane', 7.0, 5.1, 8.4, 8.9),
(2, 'Franklin', 'Nolf', 7.7, 7.2, 5.2, 8.3),
(3, 'Bell', 'Chatmon', 7.3, 8.4, 8.9, 10.0),
(4, 'Belva', 'Hansard', 6.2, 9.2, 5.8, 6.7),
(5, 'Missy', 'Days', 8.3, 10.0, 7.3, 6.7),
(6, 'Vicenta', 'Criss', 5.4, 9.5, 6.1, 9.0),
(7, 'Annelle', 'Waugh', 6.5, 7.9, 9.6, 9.3),
(8, 'Darby', 'Canty', 5.5, 9.8, 6.5, 9.0),
(9, 'Ka', 'Hawbaker', 5.7, 6.4, 5.2, 6.8),
(10, 'Alease', 'Blackston', 5.3, 7.5, 9.3, 6.0);
SELECT CONCAT(first_name,last_name)AS full_name,
AS average_gpa FROM results75 r
-- 76
 CREATE TABLE funds (
  id INT PRIMARY KEY,
  order_date DATE,
  fund_name VARCHAR(50),
  order_amount INT
);

INSERT INTO funds (id, order_date, fund_name, order_amount) VALUES
(1, '2021-09-05', 'Mid-Cap', 151),
(2, '2020-11-27', 'Small-Cap', 784),
(3, '2020-11-22', 'Multi-Cap', 761),
(4, '2020-02-26', 'Large-Cap', 778),
(5, '2020-01-04', 'Mid-Cap', 949),
(6, '2020-02-01', 'Large-Cap', 392),
(7, '2020-02-07', 'Mid-Cap', 629),
(8, '2020-06-01', 'Mid-Cap', 529),
(9, '2020-08-05', 'Large-Cap', 258),
(10, '2021-09-23', 'Mid-Cap', 739),
(11, '2020-02-14', 'Large-Cap', 563),
(12, '2019-09-29', 'Small-Cap', 817),
(13, '2019-05-11', 'Large-Cap', 121),
(14, '2019-09-18', 'Mid-Cap', 341),
(15, '2019-07-07', 'Large-Cap', 260),
(16, '2018-10-11', 'Small-Cap', 102),
(17, '2018-06-04', 'Mid-Cap', 496),
(18, '2017-07-26', 'Multi-Cap', 321),
(19, '2020-04-11', 'Small-Cap', 323),
(20, '2020-09-24', 'Multi-Cap', 938),
(21, '2020-06-16', 'Small-Cap', 668),
(22, '2021-03-08', 'Small-Cap', 733),
(23, '2021-04-27', 'Small-Cap', 597),
(24, '2020-01-11', 'Small-Cap', 767),
(25, '2021-11-14', 'Small-Cap', 489);

SELECT AS month,AS fund_name,AS total_investments 
FROM funds76 
-- 77
 CREATE TABLE traffic (
  client VARCHAR(17),
  protocol VARCHAR(64),
  traffic_in INT,
  traffic_out INT
);

INSERT INTO traffic (client, protocol, traffic_in, traffic_out) VALUES
('02-E1-80-76-EC-4B', 'BGP', 0, 234737),
('43-15-AA-26-0F-A4', 'BGP', 402860, 606565),
('90-E7-D6-14-7E-8C', 'BGP', 840772, 988197),
('FB-60-23-C1-5E-D6', 'DNS', 341155, 356569),
('4D-6D-7F-62-F4-00', 'FTP', 8346, 413322),
('09-B9-A6-46-C4-21', 'FTP', 210656, 470568),
('B1-A6A35-F2A-C2', 'FTP', 897097, 161083),
('OC-CA-68-D2-4B-F5', 'HTTP', 918793, 550403),
('A4-C6-52-01-29-C', 'HTTPS', 520856, 185387),
('95-BB-78-B0-64-2', 'POP', 150880, 423073),
('B9-C1-1B-32-55-95', 'POP', 862946, 979544),
('14-FD-F1-6E-56-7', 'SMTP', 139389, 280646),
('70-E1-2D-B1-2B-9B', 'SMTP', 163986, 450401),
('C6-F1-59-FF-5D-BE', 'SMTP', 271295, 878246),
('62-C1-0F-DA-32-A7', 'SMTP', 388933, 81625),
('41-F0-8F-B8-61-D9', 'SMTP', 752842, 253981),
('93-A3-F0-1S-75-FA', 'SSH', 496717, 599280),
('52-F2-BF-45-BA-74', 'SSH', 632534, 128765),
('87-66-B5-A5-2F-7B', 'SSH', 835441, 354950),
('CE-FC-80-F3-95-58', 'UDP', 903443, 120298);

SELECT protocol ,traffic_in,traffic_out FROM traffic77 

-- 78
CREATE TABLE customers (
  id SMALLINT PRIMARY KEY,
  first_name VARCHAR(64),
  last_name VARCHAR(64)
);

CREATE TABLE campaigns (
  id SMALLINT PRIMARY KEY,
  customer_id SMALLINT,
  name VARCHAR(64),
  FOREIGN KEY (customer_id) REFERENCES customers(id)
);

CREATE TABLE events (
  dt VARCHAR(19),
  campaign_id SMALLINT,
  status VARCHAR(64),
  FOREIGN KEY (campaign_id) REFERENCES campaigns(id)
);

INSERT INTO customers (id, first_name, last_name) VALUES
(1, 'Whitney', 'Ferrero'),
(2, 'Dickie', 'Romera');

INSERT INTO campaigns (id, customer_id, name) VALUES
(1, 1, 'Upton Group'),
(2, 1, 'Roob, Hudson and Rippin'),
(3, 1, 'McCullough, Rempel and Larson'),
(4, 1, 'Lang and Sons'),
(5, 2, 'Ruecker, Hand and Haley');

INSERT INTO events (dt, campaign_id, status) VALUES
('2021-12-02 13:52:00', 1, 'failure'),
('2021-12-02 08:17:48', 2, 'failure'),
('2021-12-02 08:18:17', 2, 'failure'),
('2021-12-01 11:55:32', 3, 'failure'),
('2021-12-01 06:53:16', 4, 'failure'),
('2021-12-02 04:51:09', 4, 'failure'),
('2021-12-01 06:34:04', 5, 'failure'),
('2021-12-02 03:21:18', 5, 'failure'),
('2021-12-01 03:18:24', 5, 'failure'),
('2021-12-02 15:32:37', 1, 'success'),
('2021-12-01 04:23:20', 1, 'success'),
('2021-12-02 06:53:24', 1, 'success'),
('2021-12-02 08:01:02', 2, 'success'),
('2021-12-02 15:57:19', 2, 'success'),
('2021-12-02 16:14:34', 3, 'success'),
('2021-12-03 05:54:43', 4, 'success'),
('2021-12-03 17:56:45', 4, 'success'),
('2021-12-03 11:56:50', 4, 'success'),
('2021-12-03 06:08:20', 5, 'success');

-- 79
CREATE TABLE candidates (
  id SMALLINT PRIMARY KEY,
  first_name VARCHAR(64),
  last_name VARCHAR(64)
);

CREATE TABLE results (
  candidate_id SMALLINT,
  vote_at VARCHAR(19),
  FOREIGN KEY (candidate_id) REFERENCES candidates(id)
);

INSERT INTO candidates (id, first_name, last_name) VALUES
(1, 'Xavier', 'Ping'),
(2, 'Westley', 'Drewell'),
(3, 'Dominick', 'Scoble');

INSERT INTO results (candidate_id, vote_at) VALUES
(1, '2021-12-01 03:55:23'),
(1, '2021-12-01 21:53:26'),
(1, '2021-12-02 07:57:40'),
(1, '2021-12-02 13:56:06'),
(2, '2021-12-01 11:46:40'),
(2, '2021-12-01 14:56:05'),
(2, '2021-12-01 21:54:50'),
(2, '2021-12-02 00:43:18'),
(2, '2021-12-02 06:59:33'),
(2, '2021-12-02 08:36:35'),
(2, '2021-12-02 10:20:33'),
(2, '2021-12-02 14:02:38'),
(3, '2021-12-01 05:18:34'),
(3, '2021-12-02 03:55:37'),
(3, '2021-12-02 05:30:24'),
(3, '2021-12-02 08:32:06');

-- 80
CREATE TABLE events (
  dt VARCHAR(19),
  customer VARCHAR(64),
  amount DECIMAL(5,2)
);

INSERT INTO events (dt, customer, amount) VALUES
('2021-11-22 06:41:01', 'Donaugh Furneaux', 0.89),
('2021-12-22 20:07:04', 'Donaugh Furneaux', 10.51),
('2021-12-31 05:22:11', 'Donaugh Furneaux', 55.92),
('2021-12-12 21:26:42', 'Harley Lyddiard', 37.68),
('2021-11-22 21:24:30', 'Kippy Jelly', 85.87),
('2021-11-25 07:00:29', 'Kippy Jelly', 7.25),
('2021-12-16 16:48:32', 'Kippy Jelly', 65.49),
('2021-11-22 23:30:55', 'Latrina Jackman', 93.49),
('2021-11-24 19:38:52', 'Latrina Jackman', 82.28),
('2021-11-30 22:59:33', 'Latrina Jackman', 96.87),
('2021-12-30 13:05:34', 'Latrina Jackman', 88.19),
('2021-11-22 02:08:02', 'Maribel Braim', 20.19),
('2021-12-13 00:14:58', 'Maribel Braim', 97.99),
('2021-12-26 13:22:20', 'Maribel Braim', 57.06),
('2021-12-29 00:20:27', 'Maribel Braim', 24.35),
('2021-11-25 14:29:29', 'Orrin Curley', 6.69),
('2021-12-08 06:22:16', 'Orrin Curley', 36.85),
('2021-12-09 15:32:16', 'Orrin Curley', 11.04),
('2021-11-28 00:15:20', 'Rasla Venny', 14.59),
('2021-12-25 09:58:23', 'Rasla Venny', 6.41);




