# python

- `sys.argv` 是从1开始，argv[0]是程序名
- 如果只想在本文件中运行，不想在导入的时候运行，添加 `if __name__ == "__main__`
- `match` Notice the use of the _ symbol in the last case. This will match with any input, resulting in similar behavior as an else statement.

=== "read & write csv"

    ```py
    import csv

    students = []

    with open("1.csv") as file:
        reader = csv.DictReader(file)
        for row in reader:
            students.append({"name": row["name"], "home": row["home"]})

    for student in sorted(students, key=lambda student: student["name"]):
        print(f"{student['name']} is in {student['home']}")


    name = input("What's your name? ")
    home = input("Where's your home? ")

    with open("1.csv", "a") as file:
        writer = csv.DictWriter(file, fieldnames=["name", "home"])
        writer.writerow({"name": name, "home": home})
    ```

=== "1.csv"

    ```csv
    name,home
    Hermoine,Gryffindor
    Harry,Gryffindor
    Ron,Gryffindor
    Draco,Slytherin
    ```

=== "match"

    ```py
    name = input("What's your name? ")

    match name: 
        case "Harry":
            print("Gryffindor")
        case "Hermione":
            print("Gryffindor")
        case "Ron": 
            print("Gryffindor")
        case "Draco":
            print("Slytherin")
        case _:
            print("Who?")
    ```

=== "match update"

    ```py
    name = input("What's your name? ")

    match name: 
        case "Harry" | "Hermione" | "Ron":
            print("Gryffindor")
        case "Draco":
            print("Slytherin")
        case _:
            print("Who?")
    ```

## 概率抽取
- 假如现在要以特定概率抽取列表里元素，可以用 `random.choices(todo_list,[0.05,0.9,0.05])` 
- 但是假如要是列表抽取一个元素, `choices` 就不太方便，可以用以下方法：
```py
p = random.uniform(0, 1)

threshold = 0.8

if p >= threshold:
    print("yes")
else:
    print("no")
```

## bad smell

??? "item in list"

    额，朋友们好，我是wq。刚才有个朋友问我：人工智能群发生了什么事了？我说怎么回事。我一看，哦，原来是人工智能课出头歌实验，啪的一下，就点进来了，很快啊，上来就是一个 `judge_repeat` 的循环，然后就是一个能高达**四重**的 `judge_last` 循环，我全部防出去了啊，防出去以后自然是传统功夫以点到为止。我笑一下。补充好了直接点提交，两分多钟以后，未知的用例还没过（怀疑是汉字顺序问题），我说婷婷。

    这俩个，函数，不讲武德，来骗，来偷袭，我21岁的，小同志，这好吗？这不好。我劝，这两个，函数，好自为之，好好反思，以后，不要再犯，这样聪明，小聪明，啊，coding，要以净(clean code)为贵，要讲clean，不要搞，窝里斗，谢谢朋友们。

    更新1：🤬，能上一个用例用中文符号，下一个用例用英文符号分隔，导致卡评测的我是头一回遇到。我感觉出这个题的人应该是没有翻过python的书，有一种C++直接盲转的美😅。

    更新2：之后的好了很多，至少是会python的人负责了

    ```py
    # 没有使用好 item in list 的丑态
    def judge_repeat(value, list=[]):
        for i in range(0, len(list)):
            if (list[i] == value):
                return 1
            else:
                if (i != len(list) - 1):
                    continue
                else:
                    return 0

    def judge_last(list: list):
        for i in list:
            if i == '23':  # 食肉类
                for i in list:
                    if i == '12':  # 黄褐色
                        for i in list:
                            if i == '21':  # 哺乳类
                                for i in list:
                                    if i == '13':  # 有斑点
                                        print("黄褐色，有斑点,哺乳类，食肉类->金钱豹\n")
                                        return 0
                                    elif i == '14':  # 有黑色条纹
                                        print("黄褐色，有黑色条纹，哺乳类，食肉类->虎\n")
                                        return 0
            elif i == '24':  # 蹄类
                for i in list:
                    if i == '13':  # 有斑点
                        for i in list:
                            if i == '15':  # 长脖
                                for i in list:
                                    if i == '16':  # 长腿
                                        print("有斑点，有黑色条纹，长脖，蹄类->长颈鹿\n")
                                        return 0
    ```


