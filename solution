USE KN303_Moskvin
GO

-- 1. Выбрать из таблицы Books коды книг, 
-- названия и количество страниц (поля Code_book, Title_book и Pages),
-- отсортировать результат по названиям книг (поле Title_book по возрастанию) 
-- и по полю Pages (по убыванию).

SELECT Code_book AS [Код книги], Title_book AS [Название книги], Pages as [Количество страниц]
FROM Moskvin.Books
ORDER BY Title_book ASC, Pages DESC


-- 2. Выбрать из таблицы Books названия книг и количество страниц (поля Title_book и Pages), 
-- а из таблицы Deliveries выбрать имя соответствующего поставщика книги (поле Name_delivery).


SELECT books.Title_book AS [Название книги], books.Pages AS [Количество страниц], deliveries.Name_delivery AS [Имя поставщика книги]
FROM Moskvin.Purchases purchases
INNER JOIN Moskvin.Deliveries deliveries ON 
	purchases.Code_delivery = deliveries.Code_delivery
INNER JOIN Moskvin.Books books ON
	purchases.Code_book = books.Code_book

-- 3. Вывести список названий издательств (поле Publish) из таблицы Publishing_house, 
-- которые не находятся в городе ‘Москва' (условие по полю City).

SELECT Publish AS [Название издательства] FROM Moskvin.Publishing_house 
WHERE City != 'Москва'


-- 4. Вывести список фамилий, имен, отчеств авторов (поле Name_author) из таблицы Authors, 
-- у которых фамилия начинается на одну из букв диапазона ‘В' - ‘Г' (условие по полю Name_author).

SELECT Name_author AS [ФИО автора] 
FROM Moskvin.Authors
WHERE UPPER(Name_author) LIKE '[В-Г]%'

-- 5. Вывести названия издательств (поле Publish) из таблицы Publishing_house, 
-- которые содержат в названии сочетание ‘софт'.
SELECT Publish AS [Название издательства] 
FROM Moskvin.Publishing_house
WHERE LOWER(Publish) LIKE '%софт%'

-- 6. Вывести список названий компаний-поставщиков (поле Name_company) и названия книг (поле Title_book), 
-- которые они поставили в период с 01.01.2002 по 31.12.2003 (условие по полю Date_order).
SELECT deliveries.Name_company AS [Название компании], books.Title_book AS [Название книги] 
FROM Moskvin.Purchases purchases
	INNER JOIN Moskvin.Deliveries deliveries 
		ON purchases.Code_delivery = deliveries.Code_delivery
	INNER JOIN Moskvin.Books books
		ON purchases.Code_book = books.Code_book

WHERE Date_order BETWEEN '01.01.2002' AND '31.12.2003'

-- 7. Вывести стоимость одной печатной страницы каждой книги (использовать поля Cost и Pages)
-- и названия соответствующих книг (поле Title_book).

SELECT  books.Title_book AS [Название книги], Cost/books.Pages AS [Стоимость страницы]
FROM Moskvin.Purchases purchases
	INNER JOIN Moskvin.Books books
		ON purchases.Code_book = books.Code_book

-- 8. Вывести все сведения о поставке (все поля таблицы Purchases), 
-- а также название книги (поле Title_book) с минимальной общей стоимостью (использовать поля Cost и Amount).

SELECT Code_purchase AS [Код поставки], Date_order AS [Дата поставки], Type_purchase AS [Тип поставки], 
	Cost AS [Цена за книгу], Amount AS [Количество], Code_delivery AS [Код поставщика], books.Title_book AS [Название книги]
FROM Moskvin.Purchases purchases
	INNER JOIN Moskvin.Books AS books
		ON purchases.Code_book = books.Code_book, 
	(SELECT MIN(Cost * Amount) minTotalCost FROM Moskvin.Purchases WHERE Amount > 0) minTotalCost
WHERE (purchases.Cost * purchases.Amount) = minTotalCost

-- 9. Вывести список сделок (все поля из таблицы Purchases) 
-- за последний месяц (условие с использованием поля Date_order).

INSERT INTO Moskvin.Purchases(Code_purchase, Date_order, Type_purchase, Cost, Amount, Code_delivery, Code_book)
VALUES (1448, '2022-12-13', 1, 228, 9, 9, 21)

SELECT Code_purchase AS [Код поставки], Date_order AS [Дата поставки], Type_purchase AS [Тип поставки], 
	Cost AS [Цена за книгу], Amount AS [Количество], Code_delivery AS [Код поставщика] 
FROM Moskvin.Purchases
WHERE Date_order > DATEADD(MONTH, -1, GETUTCDATE())

-- 10. Вывести список издательств (поле Name_company), 4 -- Отепятка на какое поле надо смотреть, задание о другом.
-- книги которых были поставлены по цене 150 руб. (поле Cost).

SELECT publishing_house.Publish FROM Moskvin.Purchases purchases
	INNER JOIN Moskvin.Books books
		ON purchases.Code_book = books.Code_book
	INNER JOIN Moskvin.Publishing_house publishing_house
		ON books.Code_publish = publishing_house.Code_publish
WHERE Cost = 150

-- 11. Вывести список авторов (поле Name_author), -- Возрастом будем считать от рождения до сегодняшнего дня.
-- возраст которых меньше среднего возраста всех авторов в таблице (условие по полюBirthday).

SELECT * FROM Moskvin.Authors
SELECT Name_author AS [ФИО автора] 
FROM Moskvin.Authors, 
	(SELECT AVG(DATEDIFF(DAY, Birthday, GETUTCDATE())) avgDays FROM Moskvin.Authors) avgDays
WHERE DATEDIFF(DAY, Birthday, GETUTCDATE()) < avgDays

-- 12. Изменить в таблице Books содержимое поля Pages на 300, 
-- если код автора (поле Code_author) =56 и название книги (поле Title_book) ='Мемуары'.

UPDATE Moskvin.Books
SET Pages = 300
WHERE Code_author = 56 AND Title_book = 'Мемуары'

-- 13. Добавить в таблицу Publish_house новую запись, причем вместо ключевого поля поставить код (поле Code_publish), 
-- автоматически увеличенный на единицу от максимального кода в таблице, вместо
-- названия города - ‘Москва' (поле City), вместо издательства - ‘Наука' (поле Publish).

INSERT INTO Moskvin.Publishing_house(Code_publish, Publish, City)
VALUES((SELECT MAX(Code_publish) + 1 FROM Moskvin.Publishing_house), 'Наука', 'Москва')
GO

-- 14. Создать триггер для команд INSERT и UPDATE таблицы Books, 
-- запрещающий производить изменения для издательства, у которого не заполнено поле город.
SELECT * FROM Moskvin.Books
CREATE TRIGGER WithoutCityTrigger 
	ON Moskvin.Books 
	FOR INSERT, UPDATE AS 
		IF EXISTS (SELECT * FROM inserted source 
			INNER JOIN Moskvin.Publishing_house publishing_house
				ON source.Code_publish = publishing_house.Code_publish
			WHERE publishing_house.City = NULL)
				ROLLBACK TRANSACTION

		IF EXISTS (SELECT * FROM deleted source 
			INNER JOIN Moskvin.Publishing_house publishing_house
				ON source.Code_publish = publishing_house.Code_publish
			WHERE publishing_house.City = NULL)
				ROLLBACK TRANSACTION
GO

-- 15. Создать процедуру, которая для указанного издательства выводит информацию о продажах: 
-- Дата, Книга, Поставщик, Общая стоимость, отсортированную по дате, по каждому месяцу показать итог,
-- показать общий итог.

DROP PROCEDURE Moskvin.GetSalesInformationByPublishingHouse

CREATE PROCEDURE Moskvin.GetSalesInformationByPublishingHouse
	@Publish varchar(40)
AS 
	SELECT Date_order AS [Дата], books.Title_book AS [Название книги], deliveries.Name_delivery AS [Поставщик], 
		SUM(Cost * Amount) AS [Общая стоимость]  FROM Moskvin.Purchases purchases
		INNER JOIN Moskvin.Books books
			ON purchases.Code_book = books.Code_book
		INNER JOIN Moskvin.Publishing_house  publish
			ON books.Code_publish = publish.Code_publish
		INNER JOIN Moskvin.Deliveries deliveries 
			ON purchases.Code_delivery = deliveries.Code_delivery
	WHERE publish.Publish = @Publish
	GROUP BY Date_order, books.Title_book,  deliveries.Name_delivery
	ORDER BY Date_order DESC
GO

EXEC Moskvin.GetSalesInformationByPublishingHouse @Publish='Наука' ; 
GO  
