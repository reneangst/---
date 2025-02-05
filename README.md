// Первое задание

using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;

namespace draft
{
// Создаем наследственный класс температуры с IComparable. В ней создаем температуру в Кельвинах, указывание температуры и описание температуры
    public class Temperature : IComparable<Temperature>
{
    private double tempk;
    private string orig;
    private string desc;

    public double C => tempk - 273.15;
    public double F => (tempk - 273.15) * 9 / 5 + 32;
    public double K => tempk;
    public string Description => desc;

    // Создаем конструктор температуры, внутри которой считается описание и указывание.
    public Temperature(string tempStr, string description)
    {
        desc = description;
        orig = tempStr;

        // Разделяем строку температуры на значение и единицу измерения
        string value = tempStr.Substring(0, tempStr.Length - 1);
        char unit = tempStr[tempStr.Length - 1];

        double tempValue;

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

        if (tempk < 0)
        {
            throw new ArgumentException("Температура ниже абсолютного нуля");
        }
    }

    public override string ToString()
    {
        return $"{orig} {desc}";
    }

    public int CompareTo(Temperature other)
    {
        return tempk.CompareTo(other.tempk);
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        List<Temperature> temperatures = new List<Temperature>();

        try
        {
            // Загружаем данные
            using (StreamReader reader = new StreamReader("temperatures.txt"))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    string[] parts = line.Split(new char[] { ' ' }, 2);
                    if (parts.Length == 2)
                    {
                        try
                        {
                            Temperature temp = new Temperature(parts[0], parts[1]);
                            temperatures.Add(temp);
                        }
                        catch (ArgumentException e)
                        {
                            Console.WriteLine($"Ошибка при обработке строки '{line}': {e.Message}");
                        }
                    }
                }
            }

            // Сортировка списка
            temperatures.Sort();
            Console.WriteLine("Температуры в порядке возрастания:");
            foreach (Temperature temp in temperatures)
            {
                Console.WriteLine(temp);
            }

            // Сортировка по убыванию с использованием компаратора
            temperatures.Sort((t1, t2) => t2.K.CompareTo(t1.K));
            Console.WriteLine("\nТемпературы в порядке убывания:");
            foreach (Temperature temp in temperatures)
            {
                Console.WriteLine(temp);
            }
        }
        catch (FileNotFoundException)
        {
            Console.WriteLine("Файл temperatures.txt не найден.");
        }
        catch (Exception e)
        {
            Console.WriteLine($"Произошла ошибка: {e.Message}");
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

// Создаем класс Point. В ней добавляем x и y
public class Point
{
    public double X { get; set; }
    public double Y { get; set; }

    public Point(double x, double y)
    {
        X = x;
        Y = y;
    }

    // Создаем объявление переопределённого метода, внутри которой возвращаем строку с координатами X и Y
    public override string ToString()
    {
        return $"({X:F3}, {Y:F3})";
    }
}

// Создаем класс PointComparers. Внутри создаем наследственные классы для каждого компаратора
public class PointComparers
{
     // Компаратор для сортировки по расстоянию от начала координат
    public class Distance : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
            double dist1 = Math.Sqrt(p1.X * p1.X + p1.Y * p1.Y);
            double dist2 = Math.Sqrt(p2.X * p2.X + p2.Y * p2.Y);
            return dist1.CompareTo(dist2);
        }
    }

    // Компаратор для сортировки по расстоянию от оси абсцисс (оси X)
    public class DistanceX : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
           return Math.Abs(p1.Y).CompareTo(Math.Abs(p2.Y));
        }
    }

    // Компаратор для сортировки по расстоянию от оси ординат (оси Y)
    public class DistanceY : IComparer<Point>
    {
        public int Compare(Point p1, Point p2)
        {
            return Math.Abs(p1.X).CompareTo(Math.Abs(p2.X));
        }
    }

    // Компаратор для сортировки по расстоянию от диагонали y=x
    public class DistanceDiagonal : IComparer<Point>
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
        points.Sort(new PointComparers.Distance());
        Console.WriteLine("\nТочки, отсортированные по удалению от начала координат:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
        
         // Сортировка по расстоянию от оси абсцисс
        points.Sort(new PointComparers.DistanceX());
        Console.WriteLine("\nТочки, отсортированные по удалению от оси абсцисс:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }

        // Сортировка по расстоянию от оси ординат
        points.Sort(new PointComparers.DistanceY());
        Console.WriteLine("\nТочки, отсортированные по удалению от оси ординат:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
        
        // Сортировка по расстоянию от диагонали y=x
        points.Sort(new PointComparers.DistanceDiagonal());
        Console.WriteLine("\nТочки, отсортированные по удалению от диагонали y=x:");
        foreach (var point in points)
        {
            Console.WriteLine(point);
        }
    }
}
