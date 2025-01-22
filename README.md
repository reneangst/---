//Первое задание

using System;
using System.Collections.Generic;
using System.IO;

class Temperature : IComparable<Temperature>
{
    // Закрытые поля для хранения температуры в Кельвинах, исходного представления и описания
    private double tempk;
    private string orig;
    private string desc;

    // Конструктор класса, принимающий температуру и её описание
    public Temperature(string temperature, string description)
    {
        // Проверка корректности температуры
        if (!TryParseTemperature(temperature, out tempk))
        {
            throw new ArgumentException("Некорректное значение температуры.");
        }
        orig = temperature;
        desc = description;
    }

    // Свойство для чтения температуры в Цельсиях
    public double C => tempk - 273.15;

    // Свойство для чтения температуры в Фаренгейтах
    public double F => (tempk - 273.15) * 9 / 5 + 32;

    // Свойство для чтения температуры в Кельвинах
    public double K => tempk;

    // Свойство для чтения описания
    public string Description => desc;

    // Переопределение метода ToString для вывода исходного представления температуры и её описания
    public override string ToString()
    {
        return $"{orig} - {desc}";
    }

    // Реализация метода сравнения для интерфейса IComparable
    public int CompareTo(Temperature other)
    {
        return tempk.CompareTo(other.tempk);
    }

    // Метод для парсинга температуры из строки
    private bool TryParseTemperature(string temperature, out double kelvin)
    {
        kelvin = 0;
        // Удаляем пробелы и определяем единицу измерения
        temperature = temperature.Trim();
        string unit = temperature[^1].ToString();
        if (unit == "K")
        {
            if (double.TryParse(temperature[0..^1], out double value) && value >= 0)
            {
                kelvin = value;
                return true;
            }
        }
        else if (unit == "C")
        {
            if (double.TryParse(temperature[0..^1], out double value) && value >= -273.15)
            {
                kelvin = value + 273.15;
                return true;
            }
        }
        else if (unit == "F")
        {
            if (double.TryParse(temperature[0..^1], out double value))
            {
                kelvin = (value - 32) * 5 / 9 + 273.15;
                return kelvin >= 0; // Проверка на абсолютный ноль
            }
        }
        return false; // Некорректное значение
    }
}

class Program
{
    static void Main(string[] args)
    {
        List<Temperature> temperatures = new List<Temperature>();

        // Загрузка температурных данных из файла
        string[] lines = File.ReadAllLines("temperatures.txt");
        foreach (var line in lines)
        {
            var parts = line.Split(' ', 2); // Разделяем строку на температуру и описание
            if (parts.Length == 2)
            {
                temperatures.Add(new Temperature(parts[0], parts[1]));
            }
        }

        // Сортировка списка температур по возрастанию
        temperatures.Sort();

        // Вывод отсортированного списка
        foreach (var temp in temperatures)
        {
            Console.WriteLine(temp);
        }

        // Сортировка по убыванию с использованием компаратора
        temperatures.Sort((x, y) => y.K.CompareTo(x.K));

        // Вывод отсортированного списка по убыванию
        Console.WriteLine("\nСортировка по убыванию:");
        foreach (var temp in temperatures)
        {
            Console.WriteLine(temp);
        }
    }
}


//Второе задание

using System;
using System.Collections.Generic;
using System.Linq;

public class Point
{
    // Свойства для координат точки
    public double X { get; }
    public double Y { get; }

    // Конструктор для инициализации координат
    public Point(double x, double y)
    {
        X = x;
        Y = y;
    }

    // Переопределение метода ToString для удобного отображения точки
    public override string ToString()
    {
        return $"({X}, {Y})";
    }
}

public class PointComparerByDistanceFromOrigin : IComparer<Point>
{
    // Сравнение точек по удалению от начала координат
    public int Compare(Point p1, Point p2)
    {
        double distance1 = Math.Sqrt(p1.X * p1.X + p1.Y * p1.Y);
        double distance2 = Math.Sqrt(p2.X * p2.X + p2.Y * p2.Y);
        return distance1.CompareTo(distance2);
    }
}

public class PointComparerByDistanceFromXAxis : IComparer<Point>
{
    // Сравнение точек по удалению от оси абсцисс
    public int Compare(Point p1, Point p2)
    {
        return Math.Abs(p1.Y).CompareTo(Math.Abs(p2.Y));
    }
}

public class PointComparerByDistanceFromYAxis : IComparer<Point>
{
    // Сравнение точек по удалению от оси ординат
    public int Compare(Point p1, Point p2)
    {
        return Math.Abs(p1.X).CompareTo(Math.Abs(p2.X));
    }
}

public class PointComparerByDistanceFromDiagonal : IComparer<Point>
{
    // Сравнение точек по удалению от диагонали y=x
    public int Compare(Point p1, Point p2)
    {
        double distance1 = Math.Abs(p1.Y - p1.X);
        double distance2 = Math.Abs(p2.Y - p2.X);
        return distance1.CompareTo(distance2);
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
            double x = random.NextDouble(); // Генерация случайного числа от 0 до 1
            double y = random.NextDouble(); // Генерация случайного числа от 0 до 1
            points.Add(new Point(x, y)); // Добавление новой точки в список
        }
        return points;
    }

    public static void Main()
    {
        // Генерация 10 случайных точек
        List<Point> points = GenerateRandomPoints(10);

        // Вывод точек, упорядоченных по удалению от начала координат
        Console.WriteLine("Точки, упорядоченные по удалению от начала координат:");
        points.Sort(new PointComparerByDistanceFromOrigin());
        points.ForEach(Console.WriteLine);

        // Вывод точек, упорядоченных по удалению от оси абсцисс
        Console.WriteLine("\nТочки, упорядоченные по удалению от оси абсцисс:");
        points.Sort(new PointComparerByDistanceFromXAxis());
        points.ForEach(Console.WriteLine);

        // Вывод точек, упорядоченных по удалению от оси ординат
        Console.WriteLine("\nТочки, упорядоченные по удалению от оси ординат:");
        points.Sort(new PointComparerByDistanceFromYAxis());
        points.ForEach(Console.WriteLine);

        // Вывод точек, упорядоченных по удалению от диагонали y=x
        Console.WriteLine("\nТочки, упорядоченные по удалению от диагонали y=x:");
        points.Sort(new PointComparerByDistanceFromDiagonal());
        points.ForEach(Console.WriteLine);
    }
}
