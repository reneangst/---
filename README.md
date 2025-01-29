// Первое задание

using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;

namespace draft
{
    // Класс Temperature, реализующий IComparable<Temperature>
    public class Temperature : IComparable<Temperature>
    {
        private double tempk; // Температура в Кельвинах
        private string orig; // Исходное представление температуры
        private string desc; // Описание температуры

        // Конструктор, принимающий на вход два строковых значения: температуру и её описание
        public Temperature(string tempStr, string desc)
        {
            this.orig = tempStr;
            this.desc = desc;

            // Парсинг температуры и преобразование в Кельвины
            double tempValue;
            char unit = tempStr.Last();
            string value = tempStr.Substring(0, tempStr.Length - 1);

            if (!double.TryParse(value, NumberStyles.Any, CultureInfo.InvariantCulture, out tempValue))
            {
                throw new ArgumentException($"Некорректное значение температуры: {tempStr}");
            }

            switch (unit)
            {
                case 'C':
                    tempk = tempValue + 273.15;
                    break;
                case 'F':
                    tempk = (tempValue - 32) * 5 / 9 + 273.15;
                    break;
                case 'K':
                    tempk = tempValue;
                    break;
                default:
                    throw new ArgumentException($"Некорректная единица измерения: {unit}");
            }

            // Проверка на абсолютный ноль
            if (tempk < 0)
            {
                throw new ArgumentException($"Температура не может быть ниже абсолютного нуля: {tempk}K");
            }
        }


        // Свойства для получения температуры в разных единицах
        public double C => tempk - 273.15;
        public double F => (tempk - 273.15) * 9 / 5 + 32;
        public double K => tempk;

        // Свойство для получения описания
        public string Description => desc;

        // Переопределение метода ToString
        public override string ToString()
        {
            return $"{orig} {desc}";
        }

        // Реализация интерфейса IComparable<Temperature>
        public int CompareTo(Temperature other)
        {
            return tempk.CompareTo(other.tempk);
        }
    }

    public class Program
    {
        public static void Main(string[] args)
        {
            // Пример использования
            try
            {
                List<Temperature> temperatures = new List<Temperature>
                {
                    new Temperature("25C", "Комнатная температура"),
                    new Temperature("100F", "Температура кипения воды"),
                    new Temperature("0K", "Абсолютный ноль"),
                     new Temperature("373.15K", "Температура кипения воды"),
                    new Temperature("150C", "Температура в духовке"),
                     new Temperature("77F", "Тепло"),
                    new Temperature("10C", "Холодно"),
                     new Temperature("20C", "Прохладно"),
                     new Temperature("-10C", "Зима")
                };
                // Вывод неотсортированного списка
                Console.WriteLine("Неотсортированный список:");
                foreach (var temp in temperatures)
                {
                    Console.WriteLine(temp);
                }
                Console.WriteLine();

                // Сортировка списка по возрастанию температуры
                temperatures.Sort();

                // Вывод отсортированного списка
                Console.WriteLine("Отсортированный список (по возрастанию температуры):");
                foreach (var temp in temperatures)
                {
                    Console.WriteLine(temp);
                }
                Console.WriteLine();
               
                // Поиск самой высокой температуры
                Temperature maxTemp = temperatures.Max();
                Console.WriteLine($"Самая высокая температура: {maxTemp}");

                // Поиск самой низкой температуры
                Temperature minTemp = temperatures.Min();
                Console.WriteLine($"Самая низкая температура: {minTemp}");

                // Пример использования Linq для поиска температур выше 20C
                var hotTemperatures = temperatures.Where(temp => temp.C > 20).ToList();
                Console.WriteLine("Температуры выше 20C:");
                foreach (var temp in hotTemperatures)
                {
                    Console.WriteLine(temp);
                }
            
            }
            catch (ArgumentException ex)
            {
                Console.WriteLine($"Ошибка: {ex.Message}");
            }
             Console.ReadKey();
        }
    }
}

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

//Второе задание

using System;
using System.Collections.Generic;
using System.Linq;

public class Point
{
    public double X { get; set; }
    public double Y { get; set; }

    public Point(double x, double y)
    {
        X = x;
        Y = y;
    }

    public override string ToString()
    {
        return $"({X:F3}, {Y:F3})";
    }
}

public class PointComparers
{
     // Компаратор для сортировки по расстоянию от начала координат
    public class DistanceFromOriginComparer : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
            double dist1 = Math.Sqrt(p1.X * p1.X + p1.Y * p1.Y);
            double dist2 = Math.Sqrt(p2.X * p2.X + p2.Y * p2.Y);
            return dist1.CompareTo(dist2);
        }
    }

    // Компаратор для сортировки по расстоянию от оси абсцисс (оси X)
    public class DistanceFromXAxisComparer : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
           return Math.Abs(p1.Y).CompareTo(Math.Abs(p2.Y));
        }
    }

    // Компаратор для сортировки по расстоянию от оси ординат (оси Y)
    public class DistanceFromYAxisComparer : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
            return Math.Abs(p1.X).CompareTo(Math.Abs(p2.X));
        }
    }

    // Компаратор для сортировки по расстоянию от диагонали y=x
    public class DistanceFromDiagonalComparer : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
            double dist1 = Math.Abs(p1.Y - p1.X) / Math.Sqrt(2);
            double dist2 = Math.Abs(p2.Y - p2.X) / Math.Sqrt(2);
            return dist1.CompareTo(dist2);
        }
    }
}


public class Program
{
    // Метод для генерации случайных точек
    public static List<Point> GenerateRandomPoints(int count)
    {
        Random random = new Random();
        List<Point> points = new List<Point>();

        for (int i = 0; i < count; i++)
        {
            double x = random.NextDouble();
            double y = random.NextDouble();
            points.Add(new Point(x, y));
        }
        return points;
    }
   
    public static void Main(string[] args)
    {
        // Генерация случайных точек
        List<Point> points = GenerateRandomPoints(10);

        Console.WriteLine("Исходные точки:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }

        // Сортировка по расстоянию от начала координат
        points.Sort(new PointComparers.DistanceFromOriginComparer());
        Console.WriteLine("\nТочки, отсортированные по удалению от начала координат:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
        
         // Сортировка по расстоянию от оси абсцисс
        points.Sort(new PointComparers.DistanceFromXAxisComparer());
        Console.WriteLine("\nТочки, отсортированные по удалению от оси абсцисс:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }

        // Сортировка по расстоянию от оси ординат
        points.Sort(new PointComparers.DistanceFromYAxisComparer());
        Console.WriteLine("\nТочки, отсортированные по удалению от оси ординат:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
        
        // Сортировка по расстоянию от диагонали y=x
         points.Sort(new PointComparers.DistanceFromDiagonalComparer());
        Console.WriteLine("\nТочки, отсортированные по удалению от диагонали y=x:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
    }
}
