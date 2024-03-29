# weather app
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
  Future<WetherModel>? future;
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
      body: FutureBuilder<WetherModel>(
        future: future,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return Center(
              child: ListTile(
                leading: Text(snapshot.data!.cirtName),
                title: Text(snapshot.data!.wetgerCondition),
                subtitle: Text("${snapshot.data!.temp.toDouble()}"),
                trailing: Text(snapshot.data!.date),
              ),
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
```darrt
Future<WetherModel> getResquest() async {
  try {
    Uri url = Uri.parse(
        "https://api.weatherapi.com/v1/forecast.json?key=3bb2bcb47bfc4341a52163921240302&q=London&days=1&aqi=no&alerts=no");
    var response = await get(url);
    if (response.statusCode == 200) {
      var responseBody = jsonDecode(response.body);
      WetherModel wetherModel = WetherModel.fromJson(responseBody);
      return wetherModel;
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
class WetherModel {
  final String cirtName;
  final String date;
  final String? image;
  final double temp;
  final double maxTemp;
  final double minTemp;
  final String wetgerCondition;

  WetherModel(
      {required this.cirtName,
      required this.date,
      this.image,
      required this.temp,
      required this.maxTemp,
      required this.minTemp,
      required this.wetgerCondition});

  factory WetherModel.fromJson(json) {
    return WetherModel(
      cirtName: json["location"]["name"],
      date: json["forecast"]["forecastday"][0]["date"],
      maxTemp: json["forecast"]["forecastday"][0]["day"]["maxtemp_c"],
      minTemp: json["forecast"]["forecastday"][0]["day"]["mintemp_c"],
      temp: json["forecast"]["forecastday"][0]["day"]["avgtemp_c"],
      wetgerCondition: json["forecast"]["forecastday"][0]["day"]["condition"]
          ["text"],
      image: json["forecast"]["forecastday"][0]["day"]["condition"]["icon"],
    );
  }
}

```
