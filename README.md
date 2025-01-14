internal class Program
{
    static void Main(string[] args)
    {
        int number = 2;
        int N = 2000000000;

        double decision1 = Math.Pow(number, 2);
        int decision2 = (number * number);

        Console.WriteLine($"Результат первого решения: {decision1}");
        Console.WriteLine($"Результат второго решения: {decision2}");

        MyDateTime myDateTime1 = new MyDateTime();
        Console.WriteLine(myDateTime1.SomeMethod());

        MyDateTime myDateTime2 = new MyDateTime();
        Console.WriteLine(myDateTime2.SomeMethod());

        Console.ReadKey();
    }
}


public class MyDateTime
{

    public string SomeMethod()
    {
        return System.DateTime.Now.ToString();
    }
}
