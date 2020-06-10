---
Título: " Xamarin.Forms efeitos" Descrição: "os efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem precisar recorrer a uma implementação de processador personalizado."
MS. Prod: xamarin MS. AssetID: 8AF168A7-4CD9-4603-B961-15B8B1543784 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 03/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-effects"></a>Xamarin.FormsEffect

_As interfaces de usuário do Xamarin. Forms são renderizadas usando os controles nativos da plataforma de destino, permitindo que os Xamarin.Forms aplicativos mantenham a aparência apropriada para cada plataforma. Os efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem a necessidade de recorrer a uma implementação de processador personalizado._

## <a name="introduction-to-effects"></a>[Introdução aos efeitos](introduction.md)

Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe `PlatformEffect`.

## <a name="creating-an-effect"></a>[Criando um efeito](creating.md)

Efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor do plano de fundo do [`Entry`](xref:Xamarin.Forms.Entry) controle quando o controle ganha foco.

## <a name="passing-parameters-to-an-effect"></a>[Passar parâmetros para um efeito](passing-parameters/index.md)

Criar um efeito que é configurado por meio de parâmetros permite que o efeito seja reutilizado. Esses artigos demonstram o uso de propriedades para passar parâmetros para um efeito e a alteração de um parâmetro em runtime.

## <a name="invoking-events-from-an-effect"></a>[Invocação de eventos de um efeito](touch-tracking.md)

Efeitos podem invocar eventos. Este artigo mostra como criar um evento que implementa acompanhamento de dedos multitoque de nível baixo e sinaliza um aplicativo para pressionamentos de toque, movimentos e liberações.

## <a name="reusable-roundeffect"></a>[RoundEffect reutilizável](reusable-roundeffect.md)

RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado de Visualelement para renderizar o controle como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões circulares ou outros controles circulares.
