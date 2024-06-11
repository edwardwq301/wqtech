- [oeasy py](https://www.lanqiao.cn/courses/3584)
- [可视化](https://pythontutor.com/)

---

- [`argparse` 接收参数教程](https://tendcode.com/subject/article/python-shell/)
- 列表乘法：相当于先减一层方括号再重复，最后外面加一层方括号 `a=[1,2]*3 # a=[1,2,1,2,1,2]`
- 列表切片：如果赋给另一个变量，相当于浅复制

```py
la = [0, 1, [2, 3, 4], 5, 6, 7]
la_slice = la[0:3]
la[0] = 10
la[2].append(10)
print(la_slice)
# [0, 1, [2, 3, 4, 10]]
```

- `match` : the use of the _ symbol will match with any input

字典设置值

```py
from collection import defaultdict
s = [('yellow', 1), ('blue', 2), ('yellow', 3),
     ('blue', 4), ('red', 1)]
# 此处必须写可调用的函数像 list set，[] 不行
d = defaultdict(list) 
for k, v in s:
    d[k].append(v)
    # faster than down
    # d.setdefault(k, []).append(v)
```

### 列表
学 python 经典问题：

```py
list_a=[0,[1]]
list_b=list_a       # 和 a 完全一样
list_c=list_a.copy()# 浅复制出来一个
list_d=list_a[:]    # 浅复制出来另一个

list_a[0]=10
list_a[1].append(2)
# list_b? list_c? list_d?
```

要回答这个问题，先了解 py 中可以分成 

- mutable 可变类型：列表，集合，字典
- immutable 不可变类型：整数，字符串

浅复制一个 mutable，相当于复制指针/引用；浅复制 immutable 等于深复制，新开一块内存

[图形化展示](https://pythontutor.com/render.html#code=list_a%3D%5B0,%5B1%5D%5D%0Alist_b%3Dlist_a%0Alist_c%3Dlist_a.copy%28%29%0Alist_d%3Dlist_a%5B%3A%5D%0A%0Alist_a%5B0%5D%3D10%0Alist_a%5B1%5D.append%282%29&cumulative=false&curInstr=6&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false)

???+ note "example1"

    ```py
    def shallow_deep_part1():
        original_int = 42
        shallow_copy_int = original_int
        deep_copy_int = copy.deepcopy(original_int)
        print("immutable: shallow = deep")
        print(f"origin:\t\t{id(original_int)}\nshallow_copy:\t\t{id(shallow_copy_int)}\ndeep_copy:\t\t{id(deep_copy_int)}")

        print("\n======\n")
        print("mutable: shallow != deep")
        original_list = [1, [2, 3], 4]
        same_with_origin = original_list
        shallow_copy_list = original_list[:]

        shallow_copy_list[0] = 10
        shallow_copy_list[1].append(5)

        print("original: ", original_list)
        print("shallow: ", shallow_copy_list)
        print("same with origin: ", same_with_origin)
    ```

???+ note "example2"

    ```py
    def shallow_deep_part2():
        origin = [[0] for i in range(3)]  # each item diff
        print(id(origin[0]), id(origin[1]), id(origin[2]))
        origin[0].append(1)
        print(origin)    # [[0, 1], [0], [0]]

        another = [[0]]*3  # same item
        another[0].append(1)
        print(another)   # [[0, 1], [0, 1], [0, 1]]
    ```

### csv

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

### 概率抽取
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

### socket demo
- server: create socket, bind, listen, accept
- client: create socket, connect


=== "server"

    ```py
    import socket
    from threading import Thread

    INET = socket.AF_INET
    IP_PORT = ("127.0.0.1", 9999)
    RECEIVE_SIZE = 1024


    def acceptConnection(connection: socket, port):
        while True:
            receive_data = connection.recv(RECEIVE_SIZE).decode()
            if (receive_data == "exit"):
                break

            connection.sendall((f"server receive {port}: "+receive_data).encode())

        connection.close()


    server_socket = socket.socket(INET)
    server_socket.bind(IP_PORT)
    server_socket.listen(1)

    while True:
        client_connection, client_port = server_socket.accept()
        thread = Thread(target=acceptConnection, args=(
            client_connection, client_port))
        thread.start()

    ```

=== "client"

    ```py
    import socket

    IP_PORT = ("127.0.0.1", 9999)
    RECEIVE_SIZE = 1024
    client_socket = socket.socket(socket.AF_INET)

    client_socket.connect(IP_PORT)

    while True:
        message = input("client input: ").strip()
        if not message:
            continue
        client_socket.sendall(message.encode())

        print("get from server: ", client_socket.recv(
            RECEIVE_SIZE).decode().strip())

        if message == "exit":
            print("client: finish!")
            break

    client_socket.close()

    ```
