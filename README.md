# asupaot
-- phpMyAdmin SQL Dump
-- version 5.1.0
-- https://www.phpmyadmin.net/
--
-- Хост: 127.0.0.1:3306
-- Время создания: Окт 06 2021 г., 14:18
-- Версия сервера: 5.7.33
-- Версия PHP: 7.1.33

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- База данных: `asupaot`
--

DELIMITER $$
--
-- Процедуры
--
CREATE DEFINER=`root`@`%` PROCEDURE `insert_to_reports` ()  NO SQL
INSERT INTO `reports`( `idParalax`, `name`, `year`, `month`,  `worked_h_month`, `fixed_salary`) 
SELECT workers.idParalax , workers.name, year(date) , month(date) as month, sec_to_time(sum(worked_h_day)) as worked_h_month, workers.fixedSalary   FROM totals , workers where totals.idParalax=workers.idParalax group by  month, idParalax order by month$$

CREATE DEFINER=`root`@`%` PROCEDURE `insert_to_totals` ()  NO SQL
INSERT IGNORE INTO `totals`(`idParalax`, `start`, `finish`, `worked_h_day`, `over`, `under`, `som_day`, `date`)  
SELECT  workers.idParalax , min(time(datetime)) , max(time(datetime)) , timediff(max(time(datetime)), min(time(datetime)))-INTERVAL 1 hour , timediff(max(time(datetime)), "18:00:00") , timediff(min(time(datetime)), "09:00:00") , time_to_sec(timediff(max(time(datetime)), min(time(datetime)))) * workers.fixedSalary / 734400 , date(datetime)  FROM visits , workers where workers.idParalax=visits.idParalax group by date(datetime), idParalax 
order by datetime ASC$$

CREATE DEFINER=`root`@`%` PROCEDURE `update_reports` ()  NO SQL
UPDATE `reports` SET `total_salary`= fixed_salary/(h_month * 3600)* time_to_sec(worked_h_month)$$

DELIMITER ;

-- --------------------------------------------------------

--
-- Структура таблицы `migrations`
--

CREATE TABLE `migrations` (
  `id` int(10) UNSIGNED NOT NULL,
  `migration` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `batch` int(11) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

--
-- Дамп данных таблицы `migrations`
--

INSERT INTO `migrations` (`id`, `migration`, `batch`) VALUES
(1, '2020_03_26_120514_create_workers_table', 1),
(2, '2020_03_26_130906_create_offices_table', 2),
(3, '2020_03_26_132646_create_offices_table', 3),
(4, '2020_03_26_133712_create_posts_table', 4),
(5, '2020_03_27_111410_create_users_table', 5);

-- --------------------------------------------------------

--
-- Структура таблицы `offices`
--

CREATE TABLE `offices` (
  `idOffice` bigint(20) UNSIGNED NOT NULL,
  `nameOffice` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

--
-- Дамп данных таблицы `offices`
--

INSERT INTO `offices` (`idOffice`, `nameOffice`, `created_at`, `updated_at`) VALUES
(1, 'Бишкек', NULL, NULL),
(2, 'Нур-Султан', NULL, NULL),
(3, 'Алмата', NULL, NULL),
(4, 'Сокулук', NULL, NULL),
(5, 'Ташкент', NULL, NULL);

-- --------------------------------------------------------

--
-- Структура таблицы `posts`
--

CREATE TABLE `posts` (
  `idPost` bigint(20) UNSIGNED NOT NULL,
  `namePost` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

--
-- Дамп данных таблицы `posts`
--

INSERT INTO `posts` (`idPost`, `namePost`, `created_at`, `updated_at`) VALUES
(1, 'Помощник директора\r\n', NULL, NULL),
(2, 'Менеджер', NULL, NULL),
(3, 'Помощник бухгалтера', NULL, NULL),
(4, 'Помощник администратора', NULL, NULL),
(5, 'Курьер', NULL, NULL);

-- --------------------------------------------------------

--
-- Структура таблицы `reports`
--

CREATE TABLE `reports` (
  `idReport` int(11) NOT NULL,
  `idParalax` int(11) DEFAULT NULL,
  `name` varchar(50) COLLATE utf8mb4_unicode_ci NOT NULL,
  `year` int(11) DEFAULT NULL,
  `month` int(11) DEFAULT NULL,
  `h_month` int(11) DEFAULT '204',
  `worked_h_month` time DEFAULT NULL,
  `fixed_salary` int(11) DEFAULT NULL,
  `total_salary` int(11) DEFAULT NULL,
  `given` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
