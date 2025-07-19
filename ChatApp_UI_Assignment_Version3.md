# Flutter UI Assignment: Chat Application

**Author:** Bishal TM  
**Assignment:** Flutter UI Assignment : Chat Application

---

## 1. Widget Tree Diagram

Below is a diagram describing the widget hierarchy used for the chat application.

```
Scaffold
│
├── AppBar (custom PreferredSize)
│    └── Row
│         ├── Back Arrow IconButton
│         ├── Spacer
│         ├── Column
│         │     ├── CircleAvatar
│         │     ├── Text (User Name)
│         │     └── Text ("Active now")
│         ├── Spacer
│         ├── IconButton (Call)
│         └── IconButton (Video)
│
├── Body: Column
│    ├── Centered "Today" Chip
│    ├── Expanded
│    │    └── ListView
│    │         ├── IncomingMessageBubble
│    │         ├── OutgoingMessageBubble
│    │         ├── OutgoingAudioMessageBubble (optional)
│    │         └── ...more messages
│    └── MessageInputArea (bottom)
│         └── Row
│              ├── IconButton (Attachment)
│              ├── Expanded TextField ("Write your message")
│              ├── IconButton (Picture)
│              ├── IconButton (Camera)
│              └── IconButton (Microphone)
```

---

## 2. Main Dart Code

Below is the Dart code for the main UI screen. Place this in `lib/main.dart`.

```dart name=lib/main.dart
import 'package:flutter/material.dart';

void main() => runApp(ChatApp());

class ChatApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Chat UI Assignment',
      theme: ThemeData(primarySwatch: Colors.green),
      home: ChatScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class ChatScreen extends StatelessWidget {
  final List<Message> messages = [
    Message(
      text: "Hi Jhon! How are you?",
      isOutgoing: false,
      time: "09:20 AM",
    ),
    Message(
      text: "I'm good! What about you?",
      isOutgoing: true,
      time: "09:21 AM",
    ),
    Message(
      text: "Can you send the documents?",
      isOutgoing: false,
      time: "09:23 AM",
    ),
    Message(
      text: "Sure, I'll send them shortly.",
      isOutgoing: true,
      time: "09:25 AM",
    ),
    Message(
      isOutgoing: true,
      isAudio: true,
      time: "09:26 AM",
      audioDuration: "00:16",
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: PreferredSize(
        preferredSize: Size.fromHeight(70),
        child: CustomAppBar(),
      ),
      body: Column(
        children: [
          SizedBox(height: 10),
          Center(child: TodayChip()),
          Expanded(
            child: ListView.builder(
              padding: EdgeInsets.symmetric(vertical: 10, horizontal: 16),
              itemCount: messages.length,
              itemBuilder: (context, index) {
                final msg = messages[index];
                if (msg.isAudio) {
                  return OutgoingAudioMessageBubble(
                    duration: msg.audioDuration!,
                    time: msg.time,
                  );
                }
                if (msg.isOutgoing) {
                  return OutgoingMessageBubble(text: msg.text!, time: msg.time);
                } else {
                  return IncomingMessageBubble(text: msg.text!, time: msg.time);
                }
              },
            ),
          ),
          MessageInputArea(),
        ],
      ),
    );
  }
}

// --- AppBar ---
class CustomAppBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return AppBar(
      automaticallyImplyLeading: false,
      backgroundColor: Colors.white,
      elevation: 1,
      title: Row(
        children: [
          IconButton(
            icon: Icon(Icons.arrow_back, color: Colors.black),
            onPressed: () {},
          ),
          Expanded(
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                CircleAvatar(
                  radius: 20,
                  backgroundImage: NetworkImage(
                    'https://randomuser.me/api/portraits/men/32.jpg',
                  ),
                ),
                SizedBox(width: 10),
                Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      "Jhon Abraham",
                      style: TextStyle(
                        color: Colors.black,
                        fontWeight: FontWeight.bold,
                        fontSize: 16,
                      ),
                    ),
                    Text(
                      "Active now",
                      style: TextStyle(
                        color: Colors.green,
                        fontSize: 12,
                      ),
                    ),
                  ],
                ),
              ],
            ),
          ),
          IconButton(
            icon: Icon(Icons.call, color: Colors.black),
            onPressed: () {},
          ),
          IconButton(
            icon: Icon(Icons.videocam, color: Colors.black),
            onPressed: () {},
          ),
        ],
      ),
      centerTitle: true,
    );
  }
}

// --- Today Chip ---
class TodayChip extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Chip(
      label: Text(
        "Today",
        style: TextStyle(color: Colors.black54, fontWeight: FontWeight.bold),
      ),
      backgroundColor: Colors.grey.shade200,
      padding: EdgeInsets.symmetric(horizontal: 16, vertical: 4),
      elevation: 0,
    );
  }
}

// --- Message Model ---
class Message {
  final String? text;
  final bool isOutgoing;
  final String time;
  final bool isAudio;
  final String? audioDuration;

  Message({
    this.text,
    required this.isOutgoing,
    required this.time,
    this.isAudio = false,
    this.audioDuration,
  });
}

// --- Message Bubbles ---
class OutgoingMessageBubble extends StatelessWidget {
  final String text;
  final String time;
  OutgoingMessageBubble({required this.text, required this.time});

  @override
  Widget build(BuildContext context) {
    return Align(
      alignment: Alignment.centerRight,
      child: Container(
        margin: EdgeInsets.only(bottom: 12, left: 40),
        padding: EdgeInsets.all(12),
        decoration: BoxDecoration(
          color: Colors.green.shade400,
          borderRadius: BorderRadius.only(
            topLeft: Radius.circular(18),
            topRight: Radius.circular(18),
            bottomLeft: Radius.circular(18),
            bottomRight: Radius.circular(4),
          ),
        ),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.end,
          children: [
            Text(
              text,
              style: TextStyle(color: Colors.white, fontSize: 16),
            ),
            SizedBox(height: 4),
            Text(
              time,
              style: TextStyle(color: Colors.white70, fontSize: 11),
            ),
          ],
        ),
      ),
    );
  }
}

class IncomingMessageBubble extends StatelessWidget {
  final String text;
  final String time;
  IncomingMessageBubble({required this.text, required this.time});

  @override
  Widget build(BuildContext context) {
    return Align(
      alignment: Alignment.centerLeft,
      child: Container(
        margin: EdgeInsets.only(bottom: 12, right: 40),
        padding: EdgeInsets.all(12),
        decoration: BoxDecoration(
          color: Colors.grey.shade200,
          borderRadius: BorderRadius.only(
            topLeft: Radius.circular(18),
            topRight: Radius.circular(18),
            bottomRight: Radius.circular(18),
            bottomLeft: Radius.circular(4),
          ),
        ),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              text,
              style: TextStyle(color: Colors.black87, fontSize: 16),
            ),
            SizedBox(height: 4),
            Text(
              time,
              style: TextStyle(color: Colors.black54, fontSize: 11),
            ),
          ],
        ),
      ),
    );
  }
}

// --- Audio Message Bubble (Optional) ---
class OutgoingAudioMessageBubble extends StatelessWidget {
  final String duration;
  final String time;
  OutgoingAudioMessageBubble({required this.duration, required this.time});

  @override
  Widget build(BuildContext context) {
    return Align(
      alignment: Alignment.centerRight,
      child: Container(
        margin: EdgeInsets.only(bottom: 12, left: 40),
        padding: EdgeInsets.all(12),
        decoration: BoxDecoration(
          color: Colors.green.shade400,
          borderRadius: BorderRadius.only(
            topLeft: Radius.circular(18),
            topRight: Radius.circular(18),
            bottomLeft: Radius.circular(18),
            bottomRight: Radius.circular(4),
          ),
        ),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.end,
          children: [
            Row(
              mainAxisSize: MainAxisSize.min,
              children: [
                Icon(Icons.play_arrow, color: Colors.white),
                SizedBox(width: 8),
                // Placeholder waveform
                Container(
                  width: 60,
                  height: 24,
                  child: CustomPaint(
                    painter: WaveformPainter(),
                  ),
                ),
                SizedBox(width: 8),
                Text(
                  duration,
                  style: TextStyle(color: Colors.white, fontSize: 13),
                ),
              ],
            ),
            SizedBox(height: 4),
            Text(
              time,
              style: TextStyle(color: Colors.white70, fontSize: 11),
            ),
          ],
        ),
      ),
    );
  }
}

class WaveformPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.white70
      ..strokeWidth = 2;
    final heights = [10, 18, 14, 20, 12, 16, 15, 19, 12, 15];
    final dx = size.width / heights.length;
    for (int i = 0; i < heights.length; i++) {
      canvas.drawLine(
        Offset(i * dx, size.height),
        Offset(i * dx, size.height - heights[i]),
        paint,
      );
    }
  }
  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}

// --- Message Input Area ---
class MessageInputArea extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.white,
      padding: EdgeInsets.symmetric(vertical: 6, horizontal: 10),
      child: Row(
        children: [
          IconButton(
            icon: Icon(Icons.attach_file, color: Colors.grey.shade700),
            onPressed: () {},
          ),
          Expanded(
            child: Container(
              padding: EdgeInsets.symmetric(horizontal: 14),
              decoration: BoxDecoration(
                color: Colors.grey.shade200,
                borderRadius: BorderRadius.circular(22),
              ),
              child: TextField(
                decoration: InputDecoration(
                  border: InputBorder.none,
                  hintText: 'Write your message',
                ),
              ),
            ),
          ),
          IconButton(
            icon: Icon(Icons.photo, color: Colors.grey.shade700),
            onPressed: () {},
          ),
          IconButton(
            icon: Icon(Icons.camera_alt, color: Colors.grey.shade700),
            onPressed: () {},
          ),
          IconButton(
            icon: Icon(Icons.mic, color: Colors.green),
            onPressed: () {},
          ),
        ],
      ),
    );
  }
}
```

---

## 3. Project Structure

Recommended minimal project structure:
```
chat_app/
├── lib/
│   └── main.dart
├── pubspec.yaml
└── ...
```