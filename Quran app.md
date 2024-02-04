# Quran app
```dart
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
  Future<List<Quran>>? future;
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
      body: FutureBuilder<List<Quran>>(
        future: future,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data!.length,
              shrinkWrap: true,
              physics: const ScrollPhysics(),
              itemBuilder: (BuildContext context, int i) {
                return ListTile(
                  leading: Text("${snapshot.data![i].number.toInt()}"),
                  title: Text(snapshot.data![i].englishName),
                  subtitle: Text(snapshot.data![i].name),
                  trailing: Text(snapshot.data![i].revelationType),
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
```
# api services
```dart
Future<List<Quran>> getResquest() async {
  try {
    Uri url = Uri.parse("https://api.alquran.cloud/v1/meta");
    var response = await get(url);
    if (response.statusCode == 200) {
      var responseBody = jsonDecode(response.body);
      var speceificData = responseBody['data']['surahs']['references'];
      List<Quran> quranList = [];
      for (var quranValue in speceificData) {
        Quran quran = Quran.fromJson(quranValue);
        quranList.add(quran);
      }
      return quranList;
    } else {
      String errorMessage = jsonDecode(response.body)['error']["message"];
      log(errorMessage.toString()); //print in debug console
      throw Exception(errorMessage);
    }
  } catch (e) {
    throw Exception(e);
  }
}
```
# api model
```dart
class Quran {
  final int number;
  final String name;
  final String englishName;
  final String englishNameTranslation;
  final int numberOfAyahs;
  final String revelationType;

  Quran(
      {required this.number,
      required this.name,
      required this.englishName,
      required this.englishNameTranslation,
      required this.numberOfAyahs,
      required this.revelationType});

  factory Quran.fromJson(Map<String, dynamic> json) {
    return Quran(
        number: json['number'],
        name: json['name'],
        englishName: json['englishName'],
        englishNameTranslation: json['englishNameTranslation'],
        numberOfAyahs: json['numberOfAyahs'],
        revelationType: json['revelationType']);
  }
}
```
