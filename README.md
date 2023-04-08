<!--
This README describes the package. If you publish this package to pub.dev,
this README's contents appear on the landing page for your package.

For information about how to write a good package README, see the guide for
[writing package pages](https://dart.dev/guides/libraries/writing-package-pages).

For general information about developing packages, see the Dart guide for
[creating packages](https://dart.dev/guides/libraries/create-library-packages)
and the Flutter guide for
[developing packages and plugins](https://flutter.dev/developing-packages).
-->

## ChatGPT Application with flutter
ChatGPT is a chatbot launched by OpenAI in November 2022. It is built on top
of OpenAI's GPT-3.5 family of large language models, and is fine-tuned with both
supervised and reinforcement learning techniques.

## OpenAI Powerful Library

<br>
<p align="center">
<img alt="GitHub commit activity" src="https://img.shields.io/github/commit-activity/m/redevRx/Flutter-ChatGPT">
<img alt="GitHub contributors" src="https://img.shields.io/github/contributors/redevRx/Flutter-ChatGPT">
<img alt="GitHub Repo stars" src="https://img.shields.io/github/stars/redevRx/Flutter-ChatGPT?style=social">
<img alt="GitHub Workflow Status" src="https://img.shields.io/github/actions/workflow/status/redevRx/Flutter-ChatGPT/dart.yml?label=tests">
<img alt="GitHub Workflow Status" src="https://img.shields.io/github/actions/workflow/status/redevRx/Flutter-ChatGPT/release.yml?label=build">
<img alt="GitHub" src="https://img.shields.io/github/license/anasfik/openai">
<img alt="Pub Version" src="https://img.shields.io/pub/v/dart_openai">
<img alt="Pub Likes" src="https://img.shields.io/pub/likes/dart_openai">
<img alt="Pub Points" src="https://img.shields.io/pub/points/dart_openai">
<img alt="Pub Popularity" src="https://img.shields.io/pub/popularity/dart_openai">

</p>
</br>

## Features

- [x] [Install Package](#install-package)
- [x] [Create OpenAI Instance](#create-openai-instance)
- [x] [Change Access Token](#change-access-token)
- [x] [Complete Text](#complete-text)
- [x] [Chat Complete (GPT-3.5 Turbo)](#chat-complete-gpt-35-turbo)
- [x] [Example Q&A](#qa)
- [x] [Generate Image With Prompt](#generate-image-with-prompt)
- [x] [Editing](#edit)
- [x] [Stop Generate](#stopping-generate)
- [x] [File](#file)
- [x] [Audio](#audio)
- [x] [Model And Engine](#modelengine)
- [x] [Flutter Code Example](#flutter-example)
- [x] [Video Tutorial](#video-tutorials)



## Install Package
```dart
chat_gpt: 2.0.7
```

## Create OpenAI Instance

 - Parameter
   - Token
     - Your secret API keys are listed below. Please note that we do not display your secret API keys again after you generate them. 
     - Do not share your API key with others, or expose it in the browser or other client-side code. In order to protect the security of your account, OpenAI may also automatically rotate any API key that we've found has leaked publicly.
     - https://beta.openai.com/account/api-keys
  - OrgId
     - Identifier for this organization sometimes used in API requests
     - https://beta.openai.com/account/org-settings

```dart
final openAI = OpenAI.instance.build(token: token,baseOption: HttpSetup(receiveTimeout: const Duration(seconds: 5)),isLogger: true);
```
## Change Access Token

```dart
openAI.setToken('new-token');
```

## Complete Text
- Text Complete API
  - Translate Method
    - translateEngToThai
    - translateThaiToEng
    - translateToJapanese
  - Model
    - kTranslateModelV3
    - kTranslateModelV2
    - kCodeTranslateModelV2
      - Translate natural language to SQL queries.
      - Create code to call the Stripe API using natural language.
      - Find the time complexity of a function.
  - https://beta.openai.com/examples

- Complete with Feature

```dart
  void _translateEngToThai() async{
  final request = CompleteText(
          prompt: translateEngToThai(word: _txtWord.text.toString()),
          max_tokens: 200,
          model: kTranslateModelV3);

  final response = await openAI.onCompletion(request: request);
  
  ///cancel request
  openAI.cancelAIGenerate();
  print(response);
}
```

- Complete with FutureBuilder
```dart
Future<CTResponse?>? _translateFuture;

_translateFuture = openAI.onCompletion(request: request);

///ui code
FutureBuilder<CTResponse?>(
 future: tController.stream,
 builder: (context, snapshot) {
   final data = snapshot.data;
   if(snapshot.connectionState == ConnectionState.done) return something 
   if(snapshot.connectionState == ConnectionState.waiting) return something
   return something
})
```

- GPT-3 with SSE
```dart
  void completeWithSSE() {
  final request = CompleteText(
          prompt: "Hello world", maxTokens: 200, model: kTextDavinci3);
  openAI.onCompletionSSE(
          request: request,
          complete: (it) {
            it.map((data) => utf8.decode(data)).listen((data) {
              debugPrint("$data");
            }).onError((e) {
              ///handle error
            });
          });
}
```

## Chat Complete (GPT-3.5 Turbo)

- Support SSE(Server Send Event)
  - GPT-3.5 Turbo
```dart
  void chatCompleteWithSSE() {
  final request = ChatCompleteText(messages: [
    Map.of({"role": "user", "content": 'Hello!'})
  ], maxToken: 200, model: kChatGptTurbo0301Model);

  openAI.onChatCompletionSSE(
          request: request,
          complete: (it) {
            it.map((it) => utf8.decode(it)).listen((data) {
              debugPrint("$data");
            }).onError((e) {
              ///handle error
            });
          });
}
```
- Chat Complete

```dart
  void chatComplete() async {
    final request = ChatCompleteText(messages: [
      Map.of({"role": "user", "content": 'Hello!'})
    ], maxToken: 200, model: ChatModel.ChatGptTurbo0301Model);

    final response = await openAI.onChatCompletion(request: request);
    for (var element in response!.choices) {
      print("data -> ${element.message?.content}");
    }
  }
```
## Q&A
- Example Q&A 
  - Answer questions based on existing knowledge.
```dart
final request = CompleteText(prompt:'What is human life expectancy in the United States?'),
                model: kTranslateModelV3, maxTokens: 200);

 final response = await openAI.onCompletion(request:request);
```
- Request
 
```dart
Q: What is human life expectancy in the United States?
```

- Response

```dart
A: Human life expectancy in the United States is 78 years.
```

## Generate Image With Prompt

- Generate Image
  - prompt
    - A text description of the desired image(s). The maximum length is 1000 characters.
  - n
    - The number of images to generate. Must be between 1 and 10.
  - size
    - The size of the generated images. Must be one of 256x256, 512x512, or 1024x1024.
  - response_format
    - The format in which the generated images are returned. Must be one of url or b64_json.
  - user
    - A unique identifier representing your end-user, which can help OpenAI to monitor and detect abuse.
- 

- Generate with feature
```dart
  void _generateImage() {
  const prompt = "cat eating snake blue red.";

  final request = GenerateImage(prompt, 1,size: ImageSize.size256,
          response_format: Format.url);;
  final response = openAI.generateImage(request);
  print("img url :${response.data?.last?.url}");
}
```
## Edit
- Edit Prompt
```dart
void editPrompt() async {
    final response = await openAI.editor.prompt(EditRequest(
        model: EditModel.TextEditModel,
        input: 'What day of the wek is it?',
        instruction: 'Fix the spelling mistakes'));

    print(response.choices.last.text);
  }
```

- Edit Image
```dart
 void editImage() async {
  final response = await openAI.editor.editImage(EditImageRequest(
          image: EditFile("${image?.path}", '${image?.name}'),
          mask: EditFile('file path', 'file name'),
          size: ImageSize.size1024,
          prompt: 'King Snake'));

  print(response.data?.last?.url);
}
```

- Variations 
```dart
  void variation() async {
  final request =
  Variation(image: EditFile('${image?.path}', '${image?.name}'));
  final response = await openAI.editor.variation(request);

  print(response.data?.last?.url);
}
```
## Stopping Generate

- Stop Generate Prompt
```dart
openAI.cancelAIGenerate();
}
```

- Stop Edit
  - image
  - prompt
```dart
openAI.cancelEdit();
}
```

- Stop Embedding
```dart
openAI.cancelEmbedding();
}
```

- Stop Audio
  - translate
  - transcript
```dart
openAI.cancelAudio();
}
```

- Stop File
  - upload file
  - get file
  - delete file
```dart
openAI.cancelFile();
}
```

## File

- Get File
```dart
void getFile() async {
  final response = await openAI.file.get();
  print(response.data);
}
```

- Upload File
```dart
void uploadFile() async {
  final request = UploadFile(file: EditFile('file-path', 'file-name'),purpose: 'fine-tune');
  final response = await openAI.file.uploadFile(request);
  print(response);
}
```

- Delete File
```dart
  void delete() async {
  final response = await openAI.file.delete("file-Id");
  print(response);
}
```

- Retrieve File
```dart
  void retrieve() async {
  final response = await openAI.file.retrieve("file-Id");
  print(response);
}
```

- Retrieve Content File
```dart
  void retrieveContent() async {
  final response = await openAI.file.retrieveContent("file-Id");
  print(response);
}
```

## Audio

- Audio Translate
```dart
void audioTranslate() async {
  final mAudio = File('mp3-path');
  final request =
  AudioRequest(file: EditFile(mAudio.path, 'name'), prompt: '...');

  final response = await openAI.audio.translate(request);
}
```

- Audio Transcribe
```dart
void audioTranscribe() async {
  final mAudio = File('mp3-path');
  final request =
  AudioRequest(file: EditFile(mAudio.path, 'name'), prompt: '...');

  final response = await openAI.audio.transcribes(request);
}
```

## Model&Engine

- Model List
  - List and describe the various models available in the API. You can refer to the Models documentation to 
  understand what models are available and the differences between them.
  - https://beta.openai.com/docs/api-reference/models

```dart
final models = await openAI.listModel();
```

- Engine List
  - Lists the currently available (non-finetuned) models, and provides basic 
  information about each one such as the owner and availability.
  - https://beta.openai.com/docs/api-reference/engines

```dart
final engines = await openAI.listEngine();
```

## Flutter Example

```dart

class TranslateScreen extends StatefulWidget {
  const TranslateScreen({Key? key}) : super(key: key);
  @override
  State<TranslateScreen> createState() => _TranslateScreenState();
}

class _TranslateScreenState extends State<TranslateScreen> {
  /// text controller
  final _txtWord = TextEditingController();

  late OpenAI openAI;

  ///t => translate
  final tController = StreamController<CTResponse?>.broadcast();

  Future<CTResponse?>? _translateFuture;
  void _translateEngToThai() async {
    final request = CompleteText(
            prompt: translateEngToThai(word: _txtWord.text.toString()),
            maxTokens: 200,
            model: Model.TextDavinci3);

    _translateFuture = openAI.onCompletion(request: request);

  }

  /// ### can stop generate prompt
  void cancelAIGenerate(){
    openAI.cancelAIGenerate();
  }

  ///ID of the model to use. Currently, only and are supported
  ///[kChatGptTurboModel]
  ///[kChatGptTurbo0301Model]
  void _chatGpt3Example() async {
    final request = ChatCompleteText(messages: [
      Map.of({"role": "user", "content": 'Hello!'})
    ], maxToken: 200, model: ChatModel.ChatGptTurbo0301Model);

    final response = await openAI.onChatCompletion(request: request);
    for (var element in response!.choices) {
      print("data -> ${element.message.content}");
    }
  }


  void modelDataList() async {
    final model = await OpenAI.instance.build(token: "").listModel();
  }

  void engineList() async {
    final engines = await OpenAI.instance.build(token: "").listEngine();
  }

  void completeWithSSE() {
    final request = CompleteText(
            prompt: "Hello world", maxTokens: 200, model: Model.TextDavinci3);
    openAI.onCompletionSSE(
            request: request,
            complete: (it) {
              it.map((data) => utf8.decode(data)).listen((data) {
                ///
                final raw = data
                        .replaceAll(RegExp("data: "), '')
                        .replaceAll(RegExp("[DONE]"), '');

                /// convert data
                String message = "";
                dynamic mJson = json.decode(raw);
                if (mJson is Map) {
                  ///[message]
                  message +=
                  " ${mJson['choices'].last['text'].toString().replaceAll(RegExp("\n"), '')}";
                }
                debugPrint("${message}");
              }).onError((e) {
                ///handle error
              });
            });
  }

  void chatCompleteWithSSE() {
    final request = ChatCompleteText(messages: [
      Map.of({"role": "user", "content": 'Hello!'})
    ], maxToken: 200, model: ChatModel.ChatGptTurboModel);

    openAI.onChatCompletionSSE(
            request: request,
            complete: (it) {
              it.map((it) => utf8.decode(it)).listen((data) {
                final raw = data
                        .replaceAll(RegExp("data: "), '')
                        .replaceAll(RegExp("[DONE]"), '')
                        .replaceAll("[]", '')
                        .trim();

                if (raw != null || raw.isNotEmpty) {
                  ///
                  final mJson = json.decode(raw);
                  if (mJson is Map) {
                    debugPrint(
                            "-> :${(mJson['choices'] as List).last['delta']['content'] ?? "not fond content"}");
                  }
                }
              }).onError((e) {
                ///handle error
                debugPrint("error ---> $e");
              });
            });
  }

  @override
  void initState() {
    openAI = OpenAI.instance.build(
            token: token,
            baseOption: HttpSetup(receiveTimeout: const Duration(seconds: 20),connectTimeout: const Duration(seconds: 20)),
            isLog: true);
    super.initState();
  }

  @override
  void dispose() {
    tController.close();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    var size = MediaQuery.of(context).size;
    return Scaffold(
      backgroundColor: Colors.white,
      body: SingleChildScrollView(
        child: Center(
          child: Padding(
            padding: const EdgeInsets.symmetric(vertical: 16.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.center,
              children: [
                /**
                 * title translate
                 */
                _titleCard(size),
                /**
                 * input card
                 * insert your text for translate to th.com
                 */
                _inputCard(size),

                /**
                 * card input translate
                 */
                _resultCard(size),
                /**
                 * button translate
                 */
                _btnTranslate()
              ],
            ),
          ),
        ),
      ),
      bottomNavigationBar: _navigation(size),
    );
  }

  Widget _btnTranslate() {
    return Row(
      mainAxisAlignment: MainAxisAlignment.end,
      children: [
        Padding(
                padding: const EdgeInsets.only(right: 16.0),
                child: MaterialButtonX(
                        message: "Translate",
                        height: 40.0,
                        width: 130.0,
                        color: Colors.blueAccent,
                        icon: Icons.translate,
                        iconSize: 18.0,
                        radius: 46.0,
                        onClick: () =>  _translateEngToThai())),
      ],
    );
  }

  Widget _resultCard(Size size) {
    return FutureBuilder<CTResponse?>(
      future: _translateFuture,
      builder: (context, snapshot) {
        final text = snapshot.data?.choices.last.text ?? "Loading...";
        return Container(
          margin: const EdgeInsets.symmetric(vertical: 32.0),
          padding: const EdgeInsets.symmetric(horizontal: 16.0),
          alignment: Alignment.bottomCenter,
          width: size.width * .86,
          height: size.height * .3,
          decoration: heroCard,
          child: SingleChildScrollView(
            child: Column(
              children: [
                Text(
                  text,
                  style: const TextStyle(color: Colors.black, fontSize: 18.0),
                ),
                SizedBox(
                  width: size.width,
                  child: const Divider(
                    color: Colors.grey,
                    thickness: 1,
                  ),
                ),
                Padding(
                  padding: const EdgeInsets.all(12.0),
                  child: Row(
                    mainAxisAlignment: MainAxisAlignment.end,
                    children: const [
                      Icon(
                        Icons.copy_outlined,
                        color: Colors.grey,
                        size: 22.0,
                      ),
                      Padding(
                        padding: EdgeInsets.symmetric(horizontal: 8.0),
                        child: Icon(
                          Icons.delete_forever,
                          color: Colors.grey,
                          size: 22.0,
                        ),
                      )
                    ],
                  ),
                )
              ],
            ),
          ),
        );
      },
    );
  }

  Padding _navigation(Size size) {
    return Padding(
      padding: const EdgeInsets.symmetric(vertical: 16.0, horizontal: 18.0),
      child: Container(
        width: size.width * .8,
        height: size.height * .06,
        decoration: heroNav,
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: [
            Container(
              padding: const EdgeInsets.all(4.0),
              decoration: BoxDecoration(
                      color: Colors.indigoAccent,
                      borderRadius: BorderRadius.circular(50.0)),
              child: const Icon(
                Icons.translate,
                color: Colors.white,
                size: 22.0,
              ),
            ),
            const Icon(
              Icons.record_voice_over_outlined,
              color: Colors.indigoAccent,
              size: 22.0,
            ),
            const Icon(
              Icons.favorite,
              color: Colors.indigoAccent,
              size: 22.0,
            ),
            const Icon(
              Icons.person,
              color: Colors.indigoAccent,
              size: 22.0,
            )
          ],
        ),
      ),
    );
  }

  Widget _titleCard(Size size) {
    return Container(
      margin: const EdgeInsets.symmetric(vertical: 32.0),
      width: size.width * .86,
      height: size.height * .08,
      decoration: heroCard,
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        children: [
          Row(
            children: [
              ClipRRect(
                borderRadius: BorderRadius.circular(30),
                child: Image.network(
                  "https://www.clipartmax.com/png/middle/200-2009207_francais-english-italiano-english-flag-icon-flat.png",
                  fit: BoxFit.cover,
                  width: 30.0,
                  height: 30.0,
                ),
              ),
              const Padding(
                padding: EdgeInsets.symmetric(horizontal: 4.0),
                child: Text(
                  "Eng",
                  style: TextStyle(color: Colors.grey, fontSize: 12.0),
                ),
              ),
              Transform.rotate(
                angle: -pi / 2,
                child: const Icon(
                  Icons.arrow_back_ios_new,
                  size: 16.0,
                  color: Colors.grey,
                ),
              )
            ],
          ),
          const Padding(
            padding: EdgeInsets.symmetric(horizontal: 12.0),
            child: Icon(
              Icons.swap_horiz_outlined,
              color: Colors.grey,
              size: 22.0,
            ),
          ),
          Row(
            children: [
              ClipRRect(
                borderRadius: BorderRadius.circular(30),
                child: Image.network(
                  "https://cdn-icons-png.flaticon.com/512/197/197452.png",
                  fit: BoxFit.cover,
                  width: 30.0,
                  height: 30.0,
                ),
              ),
              const Padding(
                padding: EdgeInsets.symmetric(horizontal: 4.0),
                child: Text(
                  "Thai",
                  style: TextStyle(color: Colors.grey, fontSize: 12.0),
                ),
              ),
              Transform.rotate(
                angle: -pi / 2,
                child: const Icon(
                  Icons.arrow_back_ios_new,
                  size: 16.0,
                  color: Colors.grey,
                ),
              )
            ],
          )
        ],
      ),
    );
  }

  Widget _inputCard(Size size) {
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16.0),
      alignment: Alignment.bottomCenter,
      width: size.width * .86,
      height: size.height * .25,
      decoration: heroCard,
      child: SingleChildScrollView(
        child: Column(
          children: [
            TextField(
              decoration: const InputDecoration(
                      border: InputBorder.none,
                      enabledBorder: InputBorder.none,
                      disabledBorder: InputBorder.none),
              controller: _txtWord,
              maxLines: 6,
              textInputAction: TextInputAction.newline,
              keyboardType: TextInputType.multiline,
            ),
            SizedBox(
              width: size.width,
              child: const Divider(
                color: Colors.grey,
                thickness: 1,
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(12.0),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.end,
                children: const [
                  Icon(
                    Icons.copy_outlined,
                    color: Colors.grey,
                    size: 22.0,
                  ),
                  Padding(
                    padding: EdgeInsets.symmetric(horizontal: 8.0),
                    child: Icon(
                      Icons.record_voice_over_outlined,
                      color: Colors.grey,
                      size: 22.0,
                    ),
                  )
                ],
              ),
            )
          ],
        ),
      ),
    );
  }
}

```

<img src="https://scontent.fkkc3-1.fna.fbcdn.net/v/t39.30808-6/321956306_528473869217638_4959635231571092650_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=730e14&_nc_ohc=YumrmcfO7jAAX9N9Ygd&tn=aWCijFs0IEeQXzfE&_nc_ht=scontent.fkkc3-1.fna&oh=00_AfCQk9ebz2qnPl2pshugchDnaEXMPe6rogXpdzc3UCfcmg&oe=63EF77E4" width="350" height="760">

## Video Tutorials
 - <a href='https://www.youtube.com/watch?v=qUEUMxGW_0Q&ab_channel=idealBy'>Flutter Chat bot</a>

 - <a href='https://www.youtube.com/watch?v=z25HfnEi2zQ&t=1s&ab_channel=idealBy'>Flutter Generate Image</a>
 
