# Currency app
```dart
import 'dart:convert';
import 'dart:developer';
import 'package:api/quran/quran1/data.dart';
import 'package:api/quran/quran1/reference.dart';
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
  Future<List<Currency>>? future;
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
      body: FutureBuilder<List<Currency>>(
        future: future,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data!.length,
              shrinkWrap: true,
              physics: const ScrollPhysics(),
              itemBuilder: (BuildContext context, int i) {
                return ListTile(
                  leading: Text("${snapshot.data![i].rounding.toInt()}"),
                  title: Text(snapshot.data![i].name.toString()),
                  subtitle: Text(snapshot.data![i].name.toString()),
                  trailing: Text(snapshot.data![i].symbol.toString()),
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
Future<List<Currency>> getResquest() async {
  try {
    Uri url = Uri.parse(
        "https://api.freecurrencyapi.com/v1/currencies?apikey=fca_live_Bfqfh0mMemB7r0rtSRLjINMGl9D9ADwdgv69Gx4Y");
    var response = await get(url);
    if (response.statusCode == 200) {
      var responseBody = jsonDecode(response.body);
      Map<String, dynamic> speceificData = responseBody['data'];
      List<Currency> currencyList = [];
      for (var currencyValue in speceificData.values) {
        Currency currencyGenerate = Currency.fromJson(currencyValue);
        currencyList.add(currencyGenerate);
      }
      return currencyList;
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
class Currency {
  Currency({
    required this.symbol,
    required this.name,
    required this.symbolNative,
    required this.decimalDigits,
    required this.rounding,
    required this.code,
    required this.namePlural,
    required this.type,
  });
  final String symbol;
  final String name;
  final String symbolNative;
  final int decimalDigits;
  final int rounding;
  final String code;
  final String namePlural;
  final String type;

  factory Currency.fromJson(Map<String, dynamic> json) {
    return Currency(
        symbol: json['symbol'],
        symbolNative: json['symbol_native'],
        name: json['name'],
        decimalDigits: json['decimal_digits'],
        rounding: json['rounding'],
        type: json['type'],
        code: json['code'],
        namePlural: json['name_plural']);
  }
}
```
