// ignore_for_file: unused_local_variable

import 'dart:convert';
import 'dart:developer';
import 'package:flutter/material.dart';
import 'package:http/http.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: 'Flutter API Demo',
      home: ApiPage(),
    );
  }
}

class ApiPage extends StatefulWidget {
  const ApiPage({super.key});

  @override
  State<ApiPage> createState() => _ApiPageState();
}

class _ApiPageState extends State<ApiPage> {
  Future<List<Post>>? future;
  @override
  void initState() {
    super.initState();
    future = getResquest();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Api"),
      ),
      body: FutureBuilder<List<Post>>(
        future: future,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data!.length,
              shrinkWrap: true,
              physics: ScrollPhysics(),
              itemBuilder: (BuildContext context, int i) {
                return ListTile(
                  leading: Text("${snapshot.data![i].id.toInt()}"),
                  title: Text(snapshot.data![i].title),
                  subtitle: Text(snapshot.data![i].body),
                );
              },
            );
          } else if (snapshot.hasError) {
            return Container();
          } else {
            return const Center(
              child: CircularProgressIndicator(),
            );
          }
        },
      ),
    );
  }
}

Future<List<Post>> getResquest() async {
  try {
    Uri url = Uri.parse("https://jsonplaceholder.typicode.com/posts");
    var response = await get(url);
    if (response.statusCode == 200) {
      var responseBody = jsonDecode(response.body);
      List<Post> postList = [];
      for (var i in responseBody) {
        Post post = Post.fromJson(i);
        postList.add(post);
      }
      return postList;
    } else {
      String errorMessage = jsonDecode(response.body)['error']["message"];
      log(errorMessage.toString()); //print in debug console
      throw Exception(errorMessage);
    }
  } catch (e) {
    throw Exception(e);
  }
}

class Post {
  final int id;
  final String title;
  final String body;

  Post({required this.id, required this.title, required this.body});
  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(id: json['id'], body: json['body'], title: json['title']);
  }
}
