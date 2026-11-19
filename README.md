# Introdução passo a passo à programação mobile com Kotlin

📖 **Leia o tutorial: [https://stahe.github.io/br-kotlin-etape-par-etape-nestjs-oct-2026/](https://stahe.github.io/br-kotlin-etape-par-etape-nestjs-oct-2026/)**

Este curso ensina como escrever um aplicativo **mobile Android nativo** com [Kotlin](https://kotlinlang.org) 2.2 e [Jetpack Compose](https://developer.android.com/compose), a biblioteca de UI declarativa do Android: um aplicativo cujas telas são renderizadas **no celular** a partir de dados JSON de um servidor.

Ele segue a progressão do curso [Introdução passo a passo ao framework mobile Flutter](https://stahe.github.io/br-flutter-etape-par-etape-sept-2026/) (que, por sua vez, é inspirado nos cursos de [React](https://stahe.github.io/br-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/br-vuejs-etape-par-etape-sept-2026/) e [Angular](https://stahe.github.io/br-angular-etape-par-etape-sept-2026/)): mesma estrutura, os mesmos 25 exemplos, mesmo servidor, mesmo estudo de caso, escritos no estilo Android.

| Curso Flutter | Curso Kotlin |
|---|---|
| Dart, widgets, um método `build()` | Kotlin, funções `@Composable` |
| `setState`, `StatefulWidget` | `remember`, `rememberSaveable`, `mutableStateOf` |
| o engine do Flutter renderiza cada pixel | Compose, o toolkit próprio do Android (Material 3) |
| `initState`, `dispose` | efeitos: `LaunchedEffect`, `DisposableEffect` |
| `InheritedWidget`, provider | `CompositionLocal`, `ViewModel`, `StateFlow` |
| go_router | Compose Navigation (rotas `@Serializable`, deep links) |
| `Future`, `Stream` | corrotinas, `Flow` |
| dicionários JSON, `intl` | `strings.xml`, o idioma do aplicativo (`setApplicationLocales`) — e os mesmos dicionários JSON |
| o pacote `http` | OkHttp e seu `CookieJar` para o cookie do token |
| `shared_preferences` | DataStore, `SharedPreferences` |
| `flutter_test` | testes instrumentados do Compose (no emulador) |

O servidor, no entanto, permanece inalterado: é o servidor JSON do aplicativo **RdvMedecins**, já utilizado pelos clientes React, Vue.js, Angular e Flutter.

## A abordagem: muitos exemplos curtos e, depois, um estudo de caso

O curso é estruturado em torno de **25 exemplos curtos**, cada um focado em um único conceito. Eles formam um único projeto do Android Studio — uma activity por exemplo — acessível por um menu.

| Capítulo | Conteúdo | Exemplos |
|---|---|---|
| Primeiros passos | um projeto Android (Gradle, manifest, activity), funções `@Composable`, estado e recomposição, eventos (gestos, rolagem, foco), todos os tipos de campo, um reducer (`sealed interface`, `when`), validação, campos que sabem se validar, formatação (`java.time`, `NumberFormat`, extensões) | 01–09 |
| Componentes | parâmetros, funções como parâmetros, composição (posicionamentos, componentes genéricos), ciclo de vida (efeitos), `CompositionLocal`, comportamentos reutilizáveis e animações, diálogo de confirmação (função suspensa) e `Snackbar` | 10–16 |
| Navegação | Compose Navigation: rotas tipadas, parâmetros, deep links, barra de abas, redirecionamento, guards | 17–18 |
| Operações assíncronas e estado compartilhado | corrotinas, `Flow`, anti-bounce, respostas obsoletas (`mapLatest`), `produceState`, `ViewModel`, `StateFlow`, DataStore, tema escuro, `SaveableStateHolder` | 19–20 |
| Internacionalização | recursos `strings.xml`, parâmetros, plurais, datas, valores, idioma do aplicativo, calendário traduzido | 21 |
| O servidor: uma caixa-preta | configuração do servidor JSON, sua API, 48 exemplos com `curl`, o que muda para um cliente mobile | – |
| Comunicação com o servidor | OkHttp, o endereço do servidor (emulador, celular, `adb reverse`), o cookie do token no celular, `kotlinx.serialization`, uma camada de acesso à API, um `ViewModel`, erros do servidor associados aos campos | 22–25 |

Cada exemplo é apresentado com seu código completo e comentado e com capturas de tela de sua execução.

## O servidor: uma caixa-preta

O servidor é o servidor NestJS das lições anteriores, cujos controllers retornam **JSON**. Esta lição o trata como uma **caixa-preta**: nós o configuramos, estudamos sua API e o consultamos com `curl`, mas não precisamos ler seu código (que é fornecido e comentado para os curiosos).

- Todos os erros têm o mesmo formato: `{ "statusCode": 409, "key": "ERRORS.LOGIN_TAKEN", "params": {...}, "fields": {...} }` — **chaves** para tradução, nunca texto simples;
- Autenticação por um token JWT em um cookie `httpOnly`: no celular, é o cliente HTTP (OkHttp e seu `CookieJar`) que o armazena e o reenvia;
- Um "modo de teste" para o CAPTCHA, que permite consultar a API com `curl` (e gerar capturas de tela por meio de testes automatizados).

## O estudo de caso: o cliente Android do RdvMedecins

Um aplicativo completo de **agendamento de consultas em um consultório médico**, com **todos** os arquivos listados e comentados.

- **Android moderno**: Kotlin 2.2, Jetpack Compose e Material 3; uma única activity; Compose Navigation; `ViewModel`; corrotinas; OkHttp; kotlinx.serialization; uma arquitetura em camadas (dados, estado, UI).
- **Três papéis**: `ADMIN` (gerencia médicos e clientes), `DOCTOR` (agenda e cancela consultas), `USER` (o paciente: agenda consultas para si mesmo, gerencia sua conta).
- **Privacidade**: um paciente nunca recebe os nomes dos outros pacientes — o servidor não os envia.
- **Todo o estado de uma tela está contido em sua rota**: `Ecrans.Agenda(idMedecin = 1, jour = "2026-10-05")`; a janela de agendamento é um destino do tipo "dialog", que o botão Voltar fecha.
- **Validação no servidor**: os formulários exibem, abaixo de cada campo, os erros retornados pela API; bloqueio otimista, nomes duplicados, login já utilizado etc.
- **Sessão**: persiste entre execuções (o cookie é armazenado no dispositivo), é restaurada na inicialização (`GET /api/auth/moi`), com a expiração tratada em um único ponto (resposta 401), e volta à tela solicitada após a reconexão.
- **Adaptado ao mobile**: menu lateral (drawer) (ou `NavigationRail` em telas grandes e no modo paisagem), listas em vez de tabelas, botão flutuante, teclado que não esconde os campos e funcionalidades que sobrevivem à rotação e às mudanças de idioma.
- **Francês / inglês**, com os dicionários JSON dos outros clientes, incluindo o calendário e os próprios textos do Android.
- **Capturas de tela automáticas**: um teste instrumentado navega pelo aplicativo como um usuário.
- **Implantação**: a versão de produção assinada (APK, Android App Bundle).

## Conteúdo do repositório

```
exemples_kotlin/           os 25 exemplos curtos (um único projeto do Android Studio)
rdvmedecins-nestjs-json/   o servidor JSON do RdvMedecins (a “caixa-preta”)
rdvmedecins_kotlin/        o cliente Android do estudo de caso
tools/                    os scripts do Windows que geram as capturas de tela
```

Cada projeto pode ser aberto no Android Studio (`File / Open`) ou compilado pela linha de comando: `gradlew installDebug`. Os aplicativos se conectam ao servidor em `http://localhost:8080`, depois de executar `adb reverse tcp:8080 tcp:8080` (ou em outro endereço: `gradlew installDebug -Pserver=http://10.0.2.2:8080`).

## Tecnologias

Kotlin 2.2 · Jetpack Compose (BOM 2026.02) · Material 3 · Navigation Compose 2.9 · Lifecycle / ViewModel 2.9 · DataStore 1.1 · AppCompat 1.7 · OkHttp 4.12 · kotlinx.serialization 1.9 · AndroidSVG 1.4 · Compose UI Test · AGP 9.4 · Gradle 9.6 · Android 8.0 (API 26) a Android 17 (API 37) · no servidor: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Pré-requisitos

- Noções básicas da linguagem Kotlin: o curso de [Kotlin](https://stahe.github.io/kotlin-oct-2026/) é uma boa preparação, mas os conceitos utilizados são explicados ao longo dos exemplos. Noções básicas do protocolo HTTP.
- O Android Studio (que fornece o JDK, o Gradle, o SDK do Android e o emulador) ou um celular Android; Node.js e um servidor MySQL (por exemplo, o Laragon no Windows) para o servidor JSON. As instruções de instalação são fornecidas no curso.

## Autor

Este curso, seus exemplos e o cliente Android foram escritos por **Claude**, a IA da [Anthropic](https://www.anthropic.com) (outubro de 2026), a pedido de **Serge Tahé**.
