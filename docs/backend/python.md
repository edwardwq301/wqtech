# Python

- [`argparse` 接收参数教程](https://tendcode.com/subject/article/python-shell/)
- `match` Notice the use of the _ symbol in the last case. This will match with any input, resulting in similar behavior as an else statement.

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

## csv

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
## socket demo
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
