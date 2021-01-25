<!-- 2021-01-23 22:59:36 -->

# 3 - Practice: YAML #
________________________________________________________________

These are the practice questions/labs included in the course. If you want to experience the real deal of configuring in the actual KodeKloud platform, you can check out their course, [Ansible for Absolute Beginners](https://kodekloud.com/p/ansible-for-the-absolute-beginners).

Note that these are not the exact same questions. You can try to answer them and create a YAML file in your own machines based on the questions.

When you're done, click 'Answer' to see if you got it correctly.

Good luck!
______________________________________________

**1. Create a dictionary with the following key-value pairs**

| Key | Value |
| :- | :- |
| name | apple |
| color | red |
| weight | 90 g |

<details>
<summary> Answer: </summary>

```yaml
name: apple
color: red
weight: 90 g
```

</details>

_________________

**2. Create the dictionary for "employee" using the data below.**

| Key | Value |
| :- | :- |
| name | jane doe |
| gender | female |
| age | 24 |

<details>
<summary> Answer:</summary>

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
```

</details>

_____________________________

**3. Using the same dictionary above, add the "address" to the "employee" dictionary:**

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
```

The details for the address are:

| Key | Value |
| :- | :- |
| city | orlando |
| state | florida |
| country | united states |

<br>

<details>
<summary> Answer:</summary>

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
    address:
        city: orlando
        state: florida
        country: united states
```

</details>

____________________________________________

**4. Create an array of 4 apples and 2 mangoes.**

<br>
<details>
<summary> Answer: </summary>

```yaml
- apple
- apple
- apple
- apple
- mango
- mango
```

</details>

__________________________________________________

**5. Create a list of fruits with their attributes based on the table below.**

| Fruit | Color | Weight |
| :- | :- | :- |
| apple | red | 90 g |
| banana | yellow | 100 g |
| melons | green | 150 g |
| apricots | orange | 75 g |

The first one is already created.

```yaml
- fruit: apple
  color: red
  weight: 90 g
```
<br>

<details>
<summary> Answer: </summary>

```yaml
- fruit: apple
  color: red
  weight: 90 g

- fruit: banana
  color: yellow
  weight: 100 g

- fruit: melons
  color: green
  weight: 150 g

- fruit: apricots
  color: orange
  weight: 75 g
```

</details>

________________________________

**6. Using the same dictionary from Question 3, add another employee named 'John Smith' and convert dictionary to a list.**

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
```

The details for john:

| Key | Value |
| :- | :- |
| name | john smith |
| gender | male |
| age | 30 |

<br>
<details>
<summary> Answer: </summary>

```yaml
employee:
  - name: jane doe
    gender: female
    age: 24

  - name: john smith
    gender: male
    age: 30

```

</details>

__________________________________________________

**7. Add the "payslip" details for Jane Doe.**

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
    address:
        city: 'orlando'
        state: 'florida'
        country: 'united states'
```
Her payslip details:

| Number | Month  | Amount |
| :- | :- | -: |
| 1 | april | 105,0000 |
| 2 | may | 120,000 |
| 3 | june | 98,000 |
| 4 | july | 110,000 |

<br>

<details>
<summary> Answer: </summary>

```yaml
employee:
    name: jane doe
    gender: female
    age: 24
    address:
        city: 'orlando'
        state: 'florida'
        country: 'united states'
    payslips:
        - number: 1
          amount: 105,000  
          month: 'april'

        - number: 2
          amount: 120,000  
          month: 'may'

        - number: 3
          amount: 98,000  
          month: 'june'

        - number: 4
          amount: 110,000  
          month: 'july'
```

</details>

_______________________________