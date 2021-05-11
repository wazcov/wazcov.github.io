---
id: 425
title: 'Java, JS &#038; Python &#8211; Pass By Value Or Reference?'
date: 2019-12-15T20:24:49+00:00
author: waz
layout: post
---
Lets take 3 of the top languages and look if they are pass-by-value or pass-by-reference

## Javascript

```
function changeStuff(a, b, c)
{
  a = a * 10;
  b.item = "changed";
  c = {item: "changed"};
}

var num = 10;
var obj1 = {item: "unchanged"};
var obj2 = {item: "unchanged"};

changeStuff(num, obj1, obj2);

console.log(num);
console.log(obj1.item);
console.log(obj2.item);
```

This produces:

```
10
changed
unchanged
```

If this was proper pass by reference, num would equal 100

Therefore we know it&#8217;s pass by value. However if we look closer, we can see that value is a reference.

We know this because if modify the element obj1, in this case making the item key &#8220;changed&#8221;, we can see it modifies the original element.

You may then be wondering why obj2 says unchanged. This is because the = operator inside changeStuff is setting c to a brand new object.

**Therefore Javascript is Pass-By-Value (But that value is often a reference).**

## Java

```
public class PassBy {
    public static void main(String[] args) {
        int a = 1;
        Baloon b = new Baloon("White");
        Baloon c = new Baloon("White");

        System.out.println(a);
        System.out.println(b.colour);
        System.out.println(c.colour);

        changeStuff(a, b, c);

        System.out.println(a);
        System.out.println(b.colour);
        System.out.println(c.colour);
    }

    static void changeStuff(int x, Baloon y, Baloon z) {
        x = x * 10;
        y.colour = "Red";
        z = new Baloon("Green");
    }

    public static class Baloon {
        public String colour = "white";
        Baloon(String colour) {
            this.colour = colour;
        }
    }

}
```

The output of this is:

```
1
White
White
1
Red
White
```

**Therefore Java, just like Javascript, is Pass-By-Value (But that value is often a reference).**

## Python

We can emulate a similar piece of code in python

```
a = 1
b = {1: "Fred"}
c = {1: "Fred"}


def changeStuff(x, y, z):
    x = x * 10
    y.update({2: "sam"})
    z = {3: "Ella"}


print(a)
print(b)
print(c)

changeStuff(a, b, c)

print(a)
print(b)
print(c)
```

This produces:

```
1
{1: 'Fred'}
{1: 'Fred'}

1
{1: 'Fred', 2: 'sam'}
{1: 'Fred'}
```

As with Java, and JavaScript, we can see the same thing. It can&#8217;t be pass by reference, else the value of a would be updated to be 100.

Therefore it must be pass-by-value. Again the value must be a reference to the original object, because we can modify its internals and see that modification outside of the function.

**Therefore Java, Javascript and Python can all be said to be Pass-By-Value (But that value is often a reference).**