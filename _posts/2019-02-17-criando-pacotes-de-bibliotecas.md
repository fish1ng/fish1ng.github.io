---
layout: post
title:  "Criando pacotes (packages) de bibliotecas"
subtitle: "As bibliotecas são uma ótima maneira de criar códigos modulares que podem ser facilmente compartilhados."
tag:
- post
- dart
comments: true
---

As bibliotecas são uma ótima maneira de criar códigos modulares que podem ser facilmente compartilhados. No ecossistema Dart, bibliotecas são criadas e distribuídas como pacotes (packages). Você com certeza já deve ter usado um.
Eles podem ser encontrados e distribuídos no [Pub: pub.dartlang.org](https://pub.dartlang.org/).

> Fato curioso: Pub de Pub mesmo, um barzinho inglês onde uma das diversões é jogar Dardos! ;) Entendeu a referência?

Os requisitos mínimos para se ter um pacote são:

- Um arquivo manifest do Pub, o `pubspec.yaml`
- Uma diretório `lib`, onde a brincadeira começa

Dentro de `lib` você pode organizar seus arquivos da manteira que achar melhor, mas por convenção o que é código de implementação e não deveria ficar público entra num sub-diretório chamado `src`.

No arquivo `pubspec.yaml` você obrigatoriamente precisa dar um nome para seu pacote, note que pacotes e bibliotecas são coisas diferentes, você pode ter *N* bibliotecas (library) dentro do seu pacote (package).

## Saindo do zero

Vamos simular aqui o pacote **dartlangbr** e nele vamos colocar uma biblioteca de validação de CPF, que é algo bem comum no universo dos desenvolvedores brasileiros. Vou criar um repositório no GitHub com o mesmo nome do pacote.

![Screenshot do GitHub](https://content.evernote.com/shard/s135/sh/e37262bf-f552-4e40-aa48-fc0d3c56df35/400c202c35bb024adffcea5ef1a1d035/res/2ec07bc8-65ee-4596-aed6-966831d2c43f/image.png)

Já podemos criar um `.gitignore` com configurações pré-definidas para um pacote Dart e escolha uma licença que fará sentido pro seu package, pra esse caso usei MIT mesmo.
Agora sem segredo, é só clonar e começar a codar:

```pwsh
> cd ~/Projects
> git clone git@github.com:leocavalcante/dartlangbr.git
> cd dartlangbr
> code .
```

### Criando nosso `pubspec.yaml`
Todo pacote no pub precisa desse arquivo de manifesto chamado `pubspec.yaml`. Um arquivo de manisfesto, no contexto de desenvolvimento, é um arquivo que contem metadados referentes a esse universo que você está apresentando, por exemplo, no pubspec.yaml, 3 metadados são obrigatórios: name, version e description. Os nomes são autoexplicativos, qual o nome do pacote, em que versão ele tá e sobre o que ele é.

```yaml
name: dartlangbr
version: 0.1.0
description: Exemplo de como criar e publicar pacotes de bibliotecas Dart no Pub.
```

Pra versão estamos usando um esquema de padronização chamado [SemVer (de Semantic Versioning)](https://semver.org/) que diz que as versões começam no 0.1.0.

Temos pouco, mas já temos algo, então bora commitar:

```pwsh
> git add .\pubspec.yaml
> git commit -m "Add Pubspec file"
```

## Criando nossa lib

Agora sim, finalmente, vamos pro Dart. Primeiro a gente cria nosso diretório de bibliotecas e depois a gente já pode começar com nossa lib de validação.

```pwsh
> mkdir lib
> code .\lib\validation.dart
```

Quando não especificado, o Dart usa o nome do próprio arquivo pra nomear a biblioteca, mas a gente pode deixar isso explícito também com a keyword library logo no começo do nosso .dart:

```dart
library validation;
```

Dentro da nossa *validation library* teremos um validator (comenta aí depois se você acha que nomes poderiam ser diferentes, se eles estão fazendo sentido pra API do pacote).
Vamos usar e abusar dos poderes de orientação a objetos do Dart, sou pessoalmente um grande fã de programação funcional, mas o paradigma do Dart é  primordialmente OO e ele é muito bom nisso, com suas raízes lá no Smalltalk, bom, chega de papo:

```dart
library validation;

abstract class Validator {
  bool validate(String subject);
}
```

Simples assim, temos nossa abstract Validator é quem vai nos servir de base para todo os validadores; temos a garantia que um validador tem um método validate que recebe uma String e retorna bool indicando true pra uma validação com sucesso ou false pra uma validação sem sucesso.

> Estamos mantendo simples aqui porque o foco é em criar um pacote, mas o ideal mesmo é ter uma classe representando o resultado da validação. Quem sabe um próximo post é sobre técnicas e princípios SOLID, GRASP e de Clean Code no Dart, comentem aí.

## Criando nossa implementação

Agora que temos nossa base, nossa fundação, podemos partir para implementação. Lembra da convenção que falamos no princípio? Então vamos manter nossa implementação fora do arquivo da biblioteca dentro do diretório lib/src. E ela vai começar com o seguinte:

###### lib/src/cpf_validator.dart

```dart
part of validation;

class CPFValidator implements Validator {
  
}
```

`part of` é a diretiva pra dizer de qual biblioteca esse arquivo faz parte, no caso, da validation library. Note que quando você estiver escrevendo seu editor provavelmente vai dizer que não reconhece a classe Validator que você está tentando implementar, mas ao invés de importa-lá, mantenha apenas o part of e lá na validation.dart nós iremos "importar" essa parte:

```dart
library validation;

part 'src/cpf_validator.dart';

abstract class Validator {
  bool validate(String subject);
}
```

Dessa vez, sem o `of`, só o `part` apontando pro nosso arquivo. Assim que você fizer isso o seu editor já vai deixar de dizer que não reconhece a Validator, porque agora ele sabe que esse arquivo faz parte da biblioteca validation e que o Validator esta nela.

Provavelmente o sue editar ainda vai apontar erros na cpf_validator.dart, mas dessa vez é sobre o método validate que ainda não foi implementado, então bora lá:

```dart
part of validation;

class CPFValidator implements Validator {
  @override
  bool validate(String subject) {
    return subject.contains('123');
  }
}
```

De novo, estamos mantando simples aqui. O foco é em criar pacotes de bibliotecas, não em validar CPF, então nosso algorítimo é só perguntar se tem 123.

**O que você acha de mandar uma PR com a validação de verdade? 😉**

## Testando nossa implementação

Ótimo, já temos uma implementação (fake, mas temos) e agora o próximo passo é criar um teste pra garantir que isso está funcionando e que vai continuar funcionando se houverem alterações.

```pwsh
> mkdir test
> code .\test\cpf_validator_test.dart
```

Quando você usa essa convenção de nomes onde o nome do diretório é test e os arquivos nele terminam com _test.dart, você já auxilia o comando pub run test que esses estão os alvos da sua bateria de testes. Esse comando vem do pacote test do pub. Inception: vamos por um pacote dentro do nosso pacote, mas vamos defini-lo como uma dependência de desenvolvimento, afinal, você não precisa rodar o teste como código de produção:

```yaml
name: dartlangbr
version: 0.1.0
description: Exemplo de como criar e publicar pacotes de bibliotecas Dart no Pub.

dev_dependencies:
  test: any
```

Trás a galera:

```pwsh
> pub get
```

Se tentar rodar agora, ele vai falar que não teste pra rodar, afinal nosso arquivo está vazio ainda.

```pwsh
> pub run test
No tests ran.
```

Bora escrever nosso teste então, na `cpf_validator_test.dart`:

```dart
import 'package:test/test.dart';
import 'package:dartlangbr/validation.dart';

void main() {
  group('Validation', () {
    test('CPF', () {
      final validator = CPFValidator();
      expect(validator.validate('123'), isTrue);
      expect(validator.validate('456'), isFalse);
    });
  });
}
```

A gente importa a biblioteca de testes, depois importa a nossa biblioteca validation que está dentro do pacote dartlangbr, cria um grupo chamado Validation, depois cria um teste pro CPF.

O teste instância o validador e roda a validação contra 123 e espera que o resultado seja true, depois roda contra 456 e espera que seja false e é isso que a gente espera mesmo que aconteça, nosso algorítimo fake só passa quando a String tem 123.

```pwsh
> pub run test
00:02 +1: All tests passed!
```

Uhu! Testes passando. Estamos prontos para:

## Publicando seu pacote

Esse comando pub, vem junto da SDK do Dart quando você instala ela. Além dos subcomandos get e run, ele também o publish, que faz exatamente isso que você deve estar imaginando:

```pwsh
> pub publish
Publishing dartlangbr 0.1.0 to https://pub.dartlang.org:
|-- .gitignore
|-- LICENSE
|-- README.md
|-- lib
|   |-- src
|   |   '-- cpf_validator.dart
|   '-- validation.dart
|-- pubspec.yaml
'-- test
    '-- cpf_validator_test.dart
Missing requirements:
* Your pubspec.yaml must have an "author" or "authors" field.
* Your pubspec.yaml is missing a "homepage" field.
* Published packages should have an upper bound constraint on the Dart SDK (typically this should restrict to less than the next major version to guard against breaking changes).
  See https://www.dartlang.org/tools/pub/pubspec#sdk-constraints for instructions on setting an sdk version constraint.
Suggestions:
* The name of "lib\validation.dart", "validation", should match the name of the package, "dartlangbr".
  This helps users know what library to import.
Sorry, your package is missing some requirements and can't be published yet.
For more information, see: https://www.dartlang.org/tools/pub/cmd/pub-lish.
```

**Opa!** Temos alguns requisitos não atendidos, faltam os metadados `author`, `homepage` e `sdk`. Vamos adicioná-los:

```yaml
name: dartlangbr
version: 0.1.0
description: Exemplo de como criar e publicar pacotes de bibliotecas Dart no Pub.
author: dartlangbr <contato@dartlangbr.com.br>
homepage: http://dartlang.com.br/

environment:
  sdk: ">=2.0.0 <3.0.0"

dev_dependencies:
  test: any
```

Tudo pronto, vamos tentar o publish de novo:

```pwsh
> pub publish
Publishing dartlangbr 0.1.0 to https://pub.dartlang.org:
|-- .gitignore
|-- LICENSE
|-- README.md
|-- lib
|   |-- src
|   |   '-- cpf_validator.dart
|   '-- validation.dart
|-- pubspec.yaml
'-- test
    '-- cpf_validator_test.dart
Suggestions:
* The name of "lib\validation.dart", "validation", should match the name of the package, "dartlangbr".
  This helps users know what library to import.

Package has 1 warning. Upload anyway (y/n)?
```

Agora temos apenas um warning, que é essa sugestão de dar o mesmo nome do pacote para a biblioteca assim os usuários já vão saber o que importar. Igual o pacote `test` tem a biblioteca `test` e o import fica: `package:test/test.dart`.

Quando você for criar o pacote pra sua lib, se só tiver uma lib dentro do pacote, provavelmente você vai querer usar o mesmo nome pro pacote mesmo, aqui usei nomes diferentes para deixar claro que pacote e biblioteca são coisas diferentes.

Nesse caso, então: **Upload anyway! (y)**

```pwsh
Package has 1 warning. Upload anyway (y/n)? y
Uploading...
Successfully uploaded package.
```

Ta lá! Nosso pacote com nossa lib está prontinho para ser usado:

[pub.dartlang.org/packages/dartlangbr](https://pub.dartlang.org/packages/dartlangbr)

Na primeira vez que você for rodar o comando publish o pub vai te redirecionar pra um ambiente OAuth com o Google, é assim que ele sabe quem tá fazendo o upload, ele usa sua conta do Gmail (ou Google Apps como é meu caso) pra identificar as pessoas que podem fazer upload.

Qualquer dúvida, não deixem de comentar e comentem também sobre temas que vocês gostaria de ver.

Obrigado!

---

**Publicado por** Leo Cavalcante
{: .notice}
