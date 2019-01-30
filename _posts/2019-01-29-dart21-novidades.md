---
layout: post
title:  "Dart 2.1"
subtitle: "O que há de novo?"
tag:
- post
- dart
comments: true
---

No dia 15 de novembro de 2018 foi anunciada a versão 2.1 da linguagem Dart. Nela, várias modificações foram feitas prezando pela redução do tamanho do código, verificação de tipos otimizada para ser processada de forma mais rápida e melhorias de usabilidade para erros de digitação. 

A versão 2 trouxe uma insanidade de novidades, principalmente com a solidificação da parte de desenvolvimento web que se adequa ao desenvolvimento de aplicações ricas para o usuário e dispositivos móveis.


Ela é uma linguagem um tanto única caso você veja da ótica que ela oferece uma máquina virtual (VM) e compilação de código tanto nativo quando JavaScript. Isso acaba trazendo suporte para a resolução de diferentes necessidades em diversas plataformas.


**Conversão dinâmica de int para double**

Os novos desenvolvedores do Flutter costumam ser enganados por erros de análise como esses ao especificar preenchimento, configurar tamanhos de fonte, etc .:

![Imagem do erro](https://cdn-images-1.medium.com/max/800/1*9hItMsF9iM2BHzbuJkHNbg.png)


Do ponto de vista do sistema, esses erros fazem sentido, afinal, a API espera um tipo (double) e o desenvolvedor especifica um valor de um tipo diferente (int). No entanto, do ponto de vista da usabilidade, parece um pouco sem sentido. Na prática, 4 ou 4.0 é a "mesma coisa", então, o time de desenvolvimento do dart implementou uma conversão "automática" do int para o double. O Dart 2.1 agora infere onde um int pode ser silenciosamente avaliado como um double e o converte.

![Imagem do tipo int sendo aceito mesmo quando se espera um double](https://cdn-images-1.medium.com/max/800/1*bJV4DQzBey1fc64nerTprg.png)


**- Melhoria nos mixins**<br>
**- Checagem de tipos em tempo de compilação**<br>
**- Melhoria de desempenho para desenvolvedores flutter**<br>
**- Melhoria de performance para desenvolvimento web (17% de redução dos arquivos dart2Js)**<br>
**- Melhoria no core da linguagem para melhorar a estabilidade e segurança**<br>

Para obter mais informações, leia o post na íntegra realizado pelo Michael Thomsen acessando [aqui](https://medium.com/dartlang/announcing-dart-2-1-improved-performance-usability-9f55fca6f31a).


---

**Publicado por** Leonardo Marinho
{: .notice}
