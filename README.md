// пятый вариант

interface IOperationResult
{
    double Result { get; }
}
interface IOperation : IOperationResult
{
    double Execute();
}
interface IOperation1 : IOperation
{
    double Execute(double x);
}
interface IOperation2 : IOperation
{
    double Execute(double x, double y);
}
interface IOperation3 : IOperation
{
    double Execute(double x, double y, double z);
}

abstract class Operation : IOperation
{
    private static List<string> _operationLog = new List<string>();
    public static IReadOnlyCollection<string> OperationLog = new ReadonlyCollection( _operationLog);

    protected double _result;
    protected List<double> _arguments = new List<double>();

    protected abstract double ArgumentsCount { get; }

    protected double this[int i]
    {
        CheckArguments();
        return _arguments[i];
    }

    public double Result => _result;
    public void AddArguments(params double[] args)
    {
        foreach (var arg in args) _arguments.Add(arg);
    }

    public abstract double Execute();

    protected virtual void CheckArguments()
    {
        if (_arguments.Count != ArgumentsCount)
        {
            throw new ArgumentException(nameof(AgrumentsCount), $"Должно быть добавлено {AgrumentsCount} аргументов!");
        }
    }

    protected virtual void Log()
    {
        _operationLog($"op {GetType().Name}({string.Join(",", _arguments)}) = {_result}");
    }
}
abstract class Operation1 : Operation, IOperation1
{
    protected override double ArgumentsCount => 1;

    public abstract double Execute(double x);
}
abstract class Operation2 : Operation, IOperation2
{
    protected override double ArgumentsCount => 2;

    public abstract double Execute(double x, double y);
}

abstract class Operation3 : Operation, IOperation3
{
    protected override double ArgumentsCount => 3;

    public abstract double Execute(double x, double y, double z);
}

class Negate : Operation1
{
    public Negate()
    {
    }

    public Negate(double x)
    {
        AddArguments(x);
    }

    public override double Execute(double x)
    {
        AddArguments(x);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = -this[0];
        return _result;
    }
}
class Plus : Operation2
{
    public Plus()
    {
    }

    public Plus(double x, double y)
    {
        AddArguments(x, y);
    }

    public override double Execute(double x, double y)
    {
        AddArguments(x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = this[0] + this[1];
        Log();
        return _result;
    }
}
class Minus : Operation2
{
    public Minus()
    {
    }

    public Minus(double x, double y)
    {
        AddArguments(x, y);
    }

    public override double Execute(double x, double y)
    {
        AddArguments(x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = this[0] - this[1];
        Log();
        return _result;
    }
}
class Multiple : Operation2
{
    public Multiple()
    {
    }

    public Multiple(double x, double y)
    {
        AddArguments(x, y);
    }

    public override double Execute(double x, double y)
    {
        AddArguments(x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = this[0] * this[1];
        Log();
        return _result;
    }
}
class Divide : Operation2
{
    public Divide()
    {
    }

    public Divide(double x, double y)
    {
        AddArguments(x, y);
    }

    public override double Execute(double x, double y)
    {
        AddArguments(x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = this[0] / this[1];
        Log();
        return _result;
    }
}

class Condition : Operation3
{
    public Condition()
    {
    }

    public Condition(double condition, double x, double y)
    {
        AddArguments(condition, x, y);
    }

    public double Execute(double condition, double x, double y)
    {
        AddArguments(condition, x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = this[0] != 0 ? this[1] : this[2];
        Log();
        return _result;
    }
}

class CustomCondition : Operation2
{
    protected readonly Func<bool> _conditionFunc;

    public CustomCondition(Func<bool> conditionFunc)
    {
        _conditionFunc = conditionFunc;
    }

    public Condition(Func<bool> conditionFunc, double x, double y)
    {
        _conditionFunc = conditionFunc;
        AddArguments(condition, x, y);
    }

    public double Execute(double x, double y)
    {
        AddArguments(x, y);
        return Execute();
    }
    public override double Execute()
    {
        CheckArguments();
        _result = _conditionFunc() ? this[0] : this[1];
        Log();
        return _result;
    }
}

var ops2 = new Operation2[] { new Plus(), new Minus() };
foreach (var op in ops2)
{
    op.Execute(10, 20);
    Console.WriteLine(op.Result);
}

var x = 1;
var y = 2;
var res = new Condition(new Plus(x, new Negate(new Minus(x, y))), new Plus(x, y), new Minus(x, y));

var ops = new Operation[] { new Negate(-10), new Plus(10, 20), new Minus(30, 10), new CustomCondition(() => true, 10, -10) };
foreach (var op in ops)
{
    op.Execute();
    Console.WriteLine(op.Result);
}
