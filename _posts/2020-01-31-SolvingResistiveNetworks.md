---
layout : "post" 
title:  "Solving Series Parallel Resistive Networks With C++"
categories : [c++, engineering]
---


A couple years ago I  took a class that showed how programming can be used to solve some engineering questions that involve math. For the final project was tasked with creating a program to solve resistive networks.

## Some background information

### What is resistance, current and voltage

For the uninitiated resistance current and voltage can be considered the foundation of all things electrical.

“The unit of electric resistance is the ohm (Ω) where one
Ohm is one volt per ampere. It is defined as the resistance
between two points in a conductor when a constant electric
potential of one volt applied at the two points produces a
current flow of one ampere in the conductor.
Thus resistance, in ohms:

![image]({{ site.baseurl }}/assets/img/resistance-resistive.png)

where V is the potential difference across the two points
in volts and I is the current flowing between the two points
in amperes.”(Bird 2007)

### What exactly does the above passage mean

To explain I will use the “water hose” analogy.
Imagine a hose carrying water connected to a pump, the hose is the conductor, the flowing water is your current, the pump moving the water through the hose is the voltage and anything impeding the flow of water is resistance.

### What is a resistive network

A resistive network is a circuit of resistors(which are simply electrical devices that impart resistance to a circuit) connected together. If a voltage is applied across the circuit there should be a current flowing through the circuit.

**Series configuration**
This means the resistors are connected end to end.
**Parallel configuration**
This means the resistors have the same start and end point.
**Series parallel**
Combination of series and configuration.

## Effect of configuration on total resistance

In series the total resistance is simply the sum of the resistance of the resistors:
The total resistance RT of some resistors in series RS<sub>1</sub>,RS<sub>2</sub>,RS<sub>N</sub> is 

### RT= RS<sub>1</sub>+RS<sub>2</sub>+RS<sub>N</sub>

In parallel the total resistance is the  
The total resistance RT of some resistors in parallel RP<sub>1</sub>,RP<sub>2</sub>,RP<sub>N</sub> is
![image]({{ site.baseurl }}/assets/img/paralleresistor.png)

If there are just two resistors in parallel the total resistance is   simply the product of both resistors divided by the sum of both resistors 
![image]{{ site.baseurl }}/(assets/img/prodsumresistor.png)

## Now onto the problem

Analyse the series parallel resistive dc circuit below to produce the total circuit resistance and the total circuit current.
The user of the program should be requested for the source voltage and the values of the resistors.
The user should also be asked for the number of series and parallel resistors in the circuit.

![image]({{ site.baseurl }}/assets/img/img-resistive-network.jpg)

## Solving the problem

```markdown
#include <iostream>

double prodsum(double a,double b);

int main()
{
    float v = 0;//voltage
    int n = 0;//number of resistors in series and parallel
    double rt = 0;//total resistance
    int count=0;
    std::cout<<"Enter the voltage of the circuit: " ;
    cin>>v;
    std::cout<<"Enter number of series and parallel resistors: "<< std::endl;
    cin>>n ;
 ```

 There are three assumptions resistance of the resistors cannot be 0 or a  negative value and there must be at least one pair of resistors so there are two loops that force the user to have inputs in line with these assumptions. 

 ```
 //Error catching while loop
    while (n <= 0)
    {
        std::cout<< "Error must have at least one resistor"<< std::endl;
        std::cout<<"Enter number of series and parallel resistors: ";
        cin>>n ;
    }
    double rs[n]= {};
    double rp[n]={};
    // While loop to enter values for resistor
    while (count<n)
    {
        std::cout<<"Enter RP"<<count+1<< ": " ;
        cin>>rp[count];
        //Error catching while loop
        while (rp[count]<=0)
        {
            std::cout<<"Error value of resistor must be greater than 0"<<std::endl ;
            std::cout<<"Enter RP"<<count+1<< ": " ;
            cin>>rp[count];
        }
        std::cout<<"Enter RS"<<count+1<< ": " ;
        cin>>rs[count];
        //Error catching while loop
        while (rs[count]<=0)
        {
            std::cout<<"Error value of resistor must be greater than 0"<<std::endl ;
            std::cout<<"Enter RS"<<count+1<< ": " ;
            cin>>rs[count];
        }
        count+=1;

    }
 ```

Resistance is calculated starting from the nth pair of resistors
Since the nth pair are in series with each other they are added together to get rt

```markdown
    //rt is the total calculated resistance
    rt=rp[n-1] + rs[n-1];
    //starts at n-2 because the total of the n-1 element resistors have already been computed
    int runs = n-2;

 ```

 Calculates remaining resistance using product sum rule using rt and rpn-1. Then adds the  rt and rs-1

 ```markdown
    while (runs>=0)
    {
        rt=prodsum(rt,rp[runs]);
        rt+=rs[runs];
        runs-=1;
    }
 ```

  calculate total current by 

 ```markdown

    double i = v/rt;
    //output total resistance and current
    std::cout<<"The total resistance is: "<< rt<<std::endl;
    std::cout <<"The total current is: "<<i ;
    return 0;
}
```

Uses product sum rule, using rt and resistor in parallel

```markdown
double prodsum(double a,double b)
{
    double c=(a*b)/(a+b);
    return c ;
}
```

## Summary

The approach taken when solving the resistance was simply to use the laws/rules governing resistors in series and parallel and work from the opposite end of the circuit from the voltage source. This was possible since the resistors would come in pairs RPn and RSn so the layout of the circuit could be predicted. First an end resistance was gotten by adding RPn and RSn which were in series. Then the total resistance of the end resistance  and the next resistance in parallel RPn-1 would be gotten using the product sum rule Rt=Ra*Rb/(Ra+Rb). Then the new end resistance would be added to RSn-1 to get a new end resistance since the total resistance of resistors in series is the sum of their individual resistance. This process was looped until only the end resistor would be left, this resistance value would be the total resistance of the circuit. The voltage is then divided by this value to get the total circuit current. There are three assumptions resistance of the resistors cannot be 0 or a  negative value and there must be at least one pair of resistors so there are two loops that force the user to have inputs in line with these assumptions. An issue is that the user must enter a number if at any point where a number is expected and the user inputs a non numeric character the program will be stuck in an infinite loop.

