using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

//Первое задание

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
            List<Temperature> temperatures = new List<Temperature>();
            string filePath = "temperatures.txt";

            try
            {
                // Загрузка данных из файла
                if (File.Exists(filePath))
                {
                    using (StreamReader reader = new StreamReader(filePath))
                    {
                        string line;
                        while ((line = reader.ReadLine()) != null)
                        {
                            string[] parts = line.Split(new char[] { ' ' }, 2, StringSplitOptions.RemoveEmptyEntries);
                            if (parts.Length == 2)
                            {
                                try
                                {
                                    temperatures.Add(new Temperature(parts[0], parts[1]));
                                }
                                catch (ArgumentException e)
                                {
                                    Console.WriteLine($"Ошибка при создании объекта температуры: {e.Message}");
                                }
                            }
                        }
                    }
                }
                else
                {
                    Console.WriteLine($"Файл {filePath} не найден.");
                    return;
                }


                Console.WriteLine("Исходный список температур:");
                foreach (var temp in temperatures)
                {
                    Console.WriteLine(temp);
                }

                // Попытка вызвать Sort (до реализации IComparable) - закомментировано, так как теперь IComparable реализован temperatures.Sort(); // Вызовет исключение до реализации IComparable<Temperature>

                // 4. Сортировка списка с использованием IComparable<T> (по возрастанию)
                temperatures.Sort();
                Console.WriteLine("\nСписок температур после сортировки по возрастанию:");
                foreach (var temp in temperatures)
                {
                    Console.WriteLine(temp);
                }

                // Сортировка с использованием компаратора (лямбда-выражение, по убыванию)
                temperatures.Sort((temp1, temp2) => temp2.K.CompareTo(temp1.K));
                Console.WriteLine("\nСписок температур после сортировки по убыванию:");
                foreach (var temp in temperatures)
                {
                    Console.WriteLine(temp);
                }
            }
            catch (Exception e)
            {
                Console.WriteLine($"Возникла ошибка: {e.Message}");
            }

            Console.ReadKey();
        }
    }
}
