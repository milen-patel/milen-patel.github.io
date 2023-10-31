---
layout: post
title: CPU From Scratch
subtitle: Here's how a computer works down to the flow of electricity
tags: [reading]
---
# Chapter XX - Introduction and Prerequisites

# Chapter XX - Binary
**Topics Covered:** Formalizing Decimal, Base-10 Meaning, Base-2 Introduction, Counting in Binary

You've likely heard of the notion that computers only understand 0's and 1's, so in this section will make sense of that.

Let's start with the number 845 which was randomly chosen for the sake of formalizing the way your mind intuitively perceives multi-digit numbers. 845, along with any other number, can be expressed in a less comapct way as a sum of it's digit positions. Instead of trying to confuse you by explaining, lets work this through by example. It should be no surprise that $845 = 800 + 40 + 5$. Similarly, the number 94 can be expressed as the sum of 90 and 4. The same decomposition can be used on even larger numbers: $29742 = 20000 + 9000+ 700 + 40 + 2$. No matter how large the example gets, your mind is able to easily verify all these statements as true by adding up all of the numbers that form their components. 

Aside: *For the next part, you will need to remember the mathematical rule that any digit raised to the power of 0 is equal to 1. That is $1^0=2^0=3^0=...=1$*

Now let's try express the same number in a different way: $845=8*10^2+4*10^1+5*10^0$
At a first glance, this may not have seem true, but with a little bit of thinking, you should be able to verify that the left hand and ride hand side of that expression are equivalent. Although it seems like you've never thought of a number this way, this is the exact way your intuition processes these larger numbers. You've probably heard of the 'ones place', 'tens place', 'hundreds place', 'thousands place', and so on. Yet, you've never seen a number be described as this visually unappealing sum of exponents being multiplied by numbers. The truth is, however, that these two concepts are the exact same. The right-most digit in a number is the ones place, and notice how $10^0=1$. Similarly, the digit second from the right is known as the tens place, and notice how $10^1 = 10$. The same logic holds for the next digit place which is known as the hundreds place, and once again notice how $10^2=100$. So really, the long expression at the start of the paragraph can be simplified to $845=8*100 + 4*10 + 5*1 = 800 + 40 + 5$. And now we're back at the original expression which was far from confusing in the previous paragraph. 

$800 = 845=8*10^2+4*10^1+5*10^0$

In this manner, you can express any number as a weighted sum of digits and corresponding powers of ten. Before we move on and explain why we are bothering with all of this, there are two more observations worth highlighting.

First, notice in any digit position, there can be one of ten distinct values: 0, 1, 2, 3, ... , 9 (Yes, 10 not 9 since 0 is a valid digit). This is our intuitive way of representing numbers and is formally called 'Base 10' or also 'Decimal'. Shortly, we will introduce a new base (don't get confused by this yet). To prevent future confusion, when we are referring to base 10 numbers (i.e. the regular way that you and I both represent numbers), I will subscript them with a 10, like so: $845_{10}$ Don't let this notation confuse you, just remember when you see that subscript, it means to treat the number the ordinary way you percieve it.

The second observation is a bit more obvious, but will shortly help you understand how counting works in other bases. The observation is that when we are counting in Base 10, if we get to the point where there is a 9 in any digit spot and we want to add one more, we get stuck because 9 is the largest value that can be stored in that position. When we run into that position, we have to 'carry-over'. More formally, that means to roll the current digit position to a 0 and then add one to the 'left' digit. So when we are counting and reach the digit 9, then the next step is to roll the 9 to a 0 and then add one to the next digit position which gives us 10. And if youre wondering what the other digit position is since 9 is a single-digit number, remember that adding 0's to the front of a value doesn't change it's meaning. That is `9=09=009`. So, in our counting example, you can say that `09` is where we begin and then to add 1, we know to roll the 9 back to a 0 and then add one to the next digit position: which is 0 so it just becomes 1. Some more examples would include `29` becoming `30` or `149` becoming `150`.

Our two step process for handling carry over is to
1. Rollback the 9 to a 0
2. Add one to the next most significant digit position (i.e. the digit position to the left)

It's worth noting that the second step can actually trigger any carryover. For example, try to add `1` to `299`. You obviously know that the answer is `300`, but observe how there are two carry over's. 

I know everything so far has been painfully obvious, but as we finally move forward, formalizing your intuition will prove helpful.

We are now ready to move on and understand how computers represent numbers. Everything so far has formalized the concept of Base 10, but for reasons that will eventually become apparent, computers cannot (easily) be built in the physical world to represent numbers the same way we do. Why not? Before I briefly explain, I would like to specify that I am no electrical engineer and any rational electrical engineer will probably label this justification as critically flawed. But, we are already operating at a low enough level, and if we went any deeper, then we would be dealing less with computer science and more with physics. Long story short, at some point, engineers have to actually build the computers they design. One ingenous idea is that we can detect the prescense or absence of electricity relatively easy. With that in mind, engineers decided to let the absence of electricty be interpreted as a 0 and the presence of electricity be represented as a 1. With these two numbers, computers have enough resources to count in 'Base 2' which is synonymous with 'Binary'.

You might be thinking, if engineers could detect the absence and presence of electricity on a wire, why could they also not detect the amount of electricity present. They could then say that no electricity at all is 0, a little bit of electricity can be interpreted as 1, a little more can be interpreted as 2, and so on, with the most amount of electricity being represented as a 9. This would effectively enable computers to count in decimal. However, it turns out that in the physical world, it is really difficult to maintain an exact 'amount' of electricity on a wire. The 'amount' of electricity on a wire can waver significantly which makes this process useless. If anything in this paragraph was slightly confusing, you don't need to worry about it. That topic transcends the scope of information I wanted to cover in this series but thought it would be worth discussing to motivate the 'Why' aspect of only having 0s and 1s.

So, if you take my word that we can use the presence of electricity to represent 0's and 1's when we are talking about electricity on a wire, then we are good to move forward. A first question: If we only have the digits 0 and 1 available, why are you calling this Base 2? Well, in base-10 (i.e. the human language), we only have the digits 0 through 9 available but we call it base-10 not Base-9. The idea being that base-K means you can have K different values in any digit position. Or equivalently stated: A digit in 'Base K' can take on values from 0 up to K-1. So, with just 0's and 1's, there are only two different values that can occupy any digit position, so we call this Base-2.

Okay, so what do numbers look like in base 2? To give you a complete answer, I will pose and then answer the following four questions. After reading and understanding the answers to each of these questions, you will know all you need to understand the black-box that is currently Binary.

1. How do you convert numbers from Base-2 to Base-10
2. How do you convert numbers from Base-10 to Base-2
3. How do you add two Base-2 numbers
4. What does counting look like in Base-2

### Converting Numbers from Base-2 to Base-10
Any combination of 0's and 1's is a valid number in base 2 (Similarly, any combination of the digits 0-9 is a valid number in base 10). Lets consider the binary number $1101_{2}$. Earlier, I mentioned that I would subscript all numbers in base-10 with a 10, so I will also subscript any base-2 numbers with a 2. Let me answer the question by giving a long form representation of $1101_{2}$.

$1101_{2} = 1*2^3 + 1*2^2 + 0*2^1 + 1*2^0$

Hopefully, this reminds you of the long-form expression from the start of this chapter when we decomposed $845_{10}$. Let me copy that below.

$845_{10}=8*10^2+4*10^1+5*10^0$

If you read over both of these expressions and recall the painfully obvious facts that I laid out earlier, you can see that the only difference between base-2 and base-10 is the value of the base in the exponentiated terms of the sum (does that explain where the word 'base' comes from!). In decimal, each of the digit places was representing a different power of 10: the ones places, tens place, hundreds place, thousands place, etc. In binary, each of the digit places is representing a different power of 2: one, two, four, eight, sixteen, thirty-two, etc.

So, lets take $1101_{2}$ and start with the right-most digit. The first power of 2 is 0 and $2^0=1$, so we multiple the value of that digit position, 1, by this power of 2: $1*1=1$. We will now do the same thing for each of the digit positions, sum up their results, and the final sum will be the decimal (i.e. the recognizable) interpretation of that number. 

Let's move to the next digit position (the second-from-the-right): the value in this spot is 0 and the next power of 2 is $2^1=2$. The product of these two values is $0*2=0$. 

Then we move onto the next digit position: the value in this spot is 1 and the next power of 2 is $2^2=4$. The product of these two values if $1*4=4$. 

Then we move onto the last digit position: the value in this spot is 1 and the next power of 2 is $2^3=8$. The product of these two values is $1*8=8$.

Lastly, we add together each of the values we computed for each of the digit positions which, in the order from right to left, is: $1 + 0 + 4 + 8 = 13$. And we did it! The binary number $1101_{2}$ which originally had no easily interpretable value, has now been deciphered as the decimal number 13. So we have that $1101_{2}=13_{10}$.

It doesn't get more complicated than that! Let's spell this out in steps:
1. Start at the right-most digit position
2. Let sum = 0 and base = 0
3. Multiple the current digit position by $2^{base}$
4. Add the result of Step 3 to sum
5. If there is another digit to process, base = base + 1 and then go back to step 3 considering the next digit position to the left.

Let's use these steps to work through one more example. Convert the value $1010101_{2}$ to Base-10. Try work this out for yourself. I will give the expanded out version instead of showing all the steps, but you should get the same result by following the steps above.

$1010101_{2} = 1*2^6 + 0*2^5 + 1*2^4 + 0*2^3 + 1*2^2 + 0*2^1 + 1*2^0$

When you simplify the right hand side of the expression (all of the values on the right side are in decimal), you will find that $1010101_{2}=85_{10}$

And with that, you now know to follow the basic steps above to convert numbers from the mysterious base-2 to the easily understandable base-10. This is not something to take lightly, it's remarkable that a random string of 0's and 1's makes no sense to us until we are able to convert it to decimal, and then we understand all there is about that number. For example, we immediately know that $\$10_{10}$ is a low amount of dollars to pay for a new shirt, but we cannot say the same about $\$10110_{2}$ with the same congitive ease. In the second scenario, you have to convert the binary number to base 10 to make any sense of it. However, as we will learn in the process of building a computer, CPU's have no understanding of numerical representation besides base-2. Whenever we are using a computer to perform arithmetic, we type in the input in base 10, but the values are immediately converted to base 2, the operations happen in base 2, and then the very last step is to return the result in base 10 so that us humans can understand it. But the average person has no idea all of that is happening under the hood, but now you do.

### Converting Numbers from Base-10 to Base-2
Converting a base 10 number to a base 2 number is a bit more difficult of a task for it doesn't always feel intuitive, but the process can be reduced down to an easy set of steps. The question I will answer in this section: what is $145_{10}$ in binary? To answer this question, I will lay out the steps for the conversion algorithm and then we will use the algorithm to convert $145_{10}$

To convert the value $X_{10}$ to binary
1. Find the largest value of K such that $2^K < X$, call this value K
2. Let result be an empty binary string ''
3. If $X - 2^k \ge 0$

    $X = X - 2^k$
    result = result + '1'
4. If $X - 2^k \lt 0 $

    result = result + '0'
5. K = K - 1
6. If $X \gt 0$ then go to Step 3 else you are done





# Chapter XX - Adding Binary Numbers and Building a 1-Bit Adder
Topics Covered: Two's Complement, Viewing Subtraction as Addition

# Chapter XX - Logic Gates
Topics Covered: AND, OR, NOR, XOR, NAND, The Universal Gate

# Chatper XX - Ripple Carry Adders and the ALU

# Chapter XX - Building a Register

# Chapter XX - Interlude: Chaining a Register to the ALU

# Chapter XX - The Clock

# Chapter XX - Interlude: Building a Counter

# Chapter XX - RAM

# Chapter XX - The Memory Address Register (MAR)

# Chapter XX - Program Counter and Computer Architecture

# Chapter XX - The BUS

# Chapter XX - Introduction to Control Unit

# Chapter XX - Instruction Register

# Chapter XX - Revisiting Control and Designing our Instruction Set

# Chapter XX - Output Register and Display

# Chapter XX - Next Steps

# Chapter XX - Latency and Throughput: Pipelining

# Chapter XX - The Three Main Principles of an Operating System

# Chapter XX - Modifying Hardware to Support an OS
Topics Covered: Interrupts, Exception Levels

# Chapter XX - Compilers

# Chapter XX - Programming Languages
Topics Covered: Grammars, Left Recursion, Parsing and Parse Trees

# Chapter XX - Designing a LISP-Like Programming Language

# Chapter XX - Building a Compiler for our Programming Language

Revisiting Operating Systems 

# Chapter XX - Multi Core CPUs
Topics Covered: Threading

# Chapter XX - Synchornizing Threads
Topics Covered: Deadlock, Spinlocks, Blocking vs Non-Blocking Locks
