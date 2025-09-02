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
