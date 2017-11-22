---
layout: post
title: C# 7.0 new features
---

C# 7.0 introduced a lot of interesting stuff. Sometimes it is better to show than to say, so this post is attempt  to show these new features on a small application.
This program is a small calculator, that can just divide numbers.

    void Main()
    {	
    	var thousand = 1_000;							//Numeric literal
    	var binary = 0b0000_0000_0000_0000_0000_1111;	//Numeric literal
    	
    	ParseTwoStrings("8", "2", "3","Div", out int var1, out int var2, out _, out CalcOperation operation);	//Out variables and discards
    	var operationObj = new Operation(operation);
    	operationObj.Execute(var1,var2, 0);
    	var (operationCopy,lastResult) = operationObj;	//Deconstructor
    	Console.WriteLine($"last operation: {operationCopy}, last result:{lastResult}");
    }
    
    public static void ParseTwoStrings(object o1, object o2, object o3, string s4, out int i1, out int i2, out int i3, out CalcOperation op)
    {
    	if (o1 is string s1)	//Patterns
    		int.TryParse(s1, out i1);
    	else
    		i1 = 0;
    
    	i2 = 0;
    	switch (o2)
    	{
    		case int i :					//Patterns
    			i2 = i;
    			break;
    		case bool b  when b == true:	//Patterns
    			i2 = 0;
    			break;
    		case string s2:
    			int.TryParse(s2, out i2);
    			break;
    		case null:
    			i2 = 0;
    			break;
    	}
    	
    	i2 = i2 != 0 ? i2 : throw new ArgumentException ("can't be 0");
    
    	if (o3 is string s3)
    		int.TryParse(s3, out i3);
    	else
    		i3 = 0;
    
    	if(!Enum.TryParse(s4, true, out op))
    		op = CalcOperation.Unknown;
    
    }
    
    public class Operation
    {
    	private CalcOperation _operation;
    	private double _lastResult;
    	public Operation(CalcOperation operation) => _operation = operation;		//expression-bodied members
    	~Operation() => Console.WriteLine("finalize");								//expression-bodied members
    	public string OperationStringName => _operation.ToString();					//expression-bodied members
    	public void Deconstruct (out CalcOperation operation, out double lastResult)	//Deconstructors
    	{
    		operation = _operation;
    		lastResult = _lastResult;
    	}
    	
    	public double Execute(int op1, int op2, int op3)
    	{
    		double r = 0;		
    		var operands = (op1,op2);									
    		var namedDperands = (FirstOperand:op1,SecondOperand:op2);		//named tuples;
    		(int someOp1, int someOp2) = GenerateOperandsTuple(op1,op2);	//implicitly deconstruction pattern
    		
    		switch (_operation)
    		{
    			case CalcOperation.Div:
    				r = Div(op1,op2);
    				break;
    		}
    		
    		Console.WriteLine($"Operands: {operands.Item1} and {namedDperands.SecondOperand}");
    		return _lastResult = r;
    		int Div(int v1, int v2) => v1 / v2;	//Local methods
    	}
    
    }
    
    
    static (int oper1, int oper2) GenerateOperandsTuple(int o1, int o2) => (o1,o2);//return multiple values without resorting to out parameters
    
    public enum CalcOperation
    {
    	Unknown,
    	Div,
    }
