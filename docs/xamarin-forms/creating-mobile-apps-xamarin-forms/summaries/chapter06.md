---
Título: "Resumo do capítulo 6. Cliques no botão "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 6. O botão clica em "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: D4F9C429-A6CF-40FA-AC68-3F149307A5F9 autor: davidbritch MS. Author: dabritch MS. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-6-button-clicks"></a>Resumo do capítulo 6. Cliques no botão

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

A [`Button`](xref:Xamarin.Forms.Button) é a exibição que permite ao usuário iniciar um comando. Um `Button` é identificado pelo texto (e, opcionalmente, uma imagem, como demonstrado no [capítulo 13, bitmaps](chapter13.md)). Consequentemente, `Button` define muitas das mesmas propriedades que `Label` :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`também define três propriedades que regem a aparência de sua borda, mas o suporte dessas propriedades e sua independência mútua são específicos da plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)do tipo`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)do tipo`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)do tipo`Double`

`Button`também herda todas as propriedades de `VisualElement` e `View` , incluindo `BackgroundColor` , `HorizontalOptions` e `VerticalOptions` .

## <a name="processing-the-click"></a>Processando o clique

A `Button` classe define um [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento que é acionado quando o usuário toca no `Button` . O `Click` manipulador é do tipo `EventHandler` . O primeiro argumento é o `Button` objeto que gera o evento; o segundo argumento é um `EventArgs` objeto que não fornece nenhuma informação adicional.

O exemplo [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) demonstra `Clicked` manipulação simples.

## <a name="sharing-button-clicks"></a>Compartilhando cliques de botão

Várias `Button` exibições podem compartilhar o mesmo `Clicked` manipulador, mas o manipulador geralmente precisa determinar qual `Button` é responsável por um evento específico. Uma abordagem é armazenar os vários `Button` objetos como campos e verificar qual deles está acionando o evento no manipulador.

O exemplo [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) demonstra essa técnica. O programa também demonstra como definir a [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade de um `Button` para `false` quando pressionar o `Button` não é mais válido. Um desabilitado não `Button` gera um `Clicked` evento.

## <a name="anonymous-event-handlers"></a>Manipuladores de eventos anônimos

É possível definir `Clicked` manipuladores como funções lambda anônimas, como demonstra o exemplo de [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . No entanto, os manipuladores anônimos não podem ser compartilhados sem um código de reflexo confuso.

## <a name="distinguishing-views-with-ids"></a>Diferenciando exibições com IDs

Vários `Button` objetos também podem ser diferenciados pela definição da [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriedade ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propriedade como um `string` . Essa propriedade é definida por `Element` , mas não é usada no Xamarin.Forms . Ele se destina a ser usado exclusivamente por programas de aplicativo.

O exemplo [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) usa o mesmo manipulador de eventos para todas as 10 chaves de número em um teclado numérico e distingue entre elas com a `StyleId` Propriedade:

[![Captura de tela tripla do teclado mais simples](images/ch06fg04-small.png "Calculator")](images/ch06fg04-large.png#lightbox "Calculator")

## <a name="saving-transient-data"></a>Salvando dados transitórios

Muitos aplicativos precisam salvar dados quando um programa é encerrado e recarregar esses dados quando o programa é iniciado novamente. A [`Application`](xref:Xamarin.Forms.Application) classe define vários membros que ajudam o programa a salvar e restaurar dados transitórios:

- A [`Properties`](xref:Xamarin.Forms.Application.Properties) propriedade é um dicionário com `string` chaves e `object` itens. O conteúdo do dicionário é salvo automaticamente no armazenamento local do aplicativo antes do encerramento do programa e recarregado quando o programa é iniciado.
- A `Application` classe define três métodos virtuais protegidos que a classe padrão do programa `App` substitui: [`OnStart`](xref:Xamarin.Forms.Application.OnStart) , [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) e [`OnResume`](xref:Xamarin.Forms.Application.OnResume) . Eles se referem a eventos de *ciclo de vida do aplicativo* .
- O [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) método salva o conteúdo do dicionário.

Não é necessário chamar `SavePropertiesAsync` . O conteúdo do dicionário é salvo automaticamente antes da conclusão do programa e recuperado antes da inicialização do programa. É útil durante o teste do programa para salvar dados se o programa falhar.

Também é útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), uma propriedade estática que retorna o `Application` objeto atual que você pode usar para obter o `Properties` dicionário.

A primeira etapa é identificar todas as variáveis na página que você deseja manter quando o programa for encerrado. Se você souber todos os locais em que essas variáveis são alteradas, poderá simplesmente adicioná-las ao `Properties` dicionário nesse ponto. No construtor da página, você pode definir as variáveis do dicionário, `Properties` se a chave existir.

Um programa maior provavelmente precisará lidar com eventos de ciclo de vida do aplicativo. O mais importante é o `OnSleep` método. Uma chamada para esse método indica que o programa saiu do primeiro plano. Talvez o usuário tenha pressionado o botão **página inicial** no dispositivo ou exibido todos os aplicativos ou esteja encerrando o telefone. Uma chamada para `OnSleep` é a única notificação que um programa recebe antes de ser encerrado. O programa deve levar essa oportunidade para garantir que o `Properties` dicionário esteja atualizado.

Uma chamada para `OnResume` indica que o programa não foi encerrado após a última chamada para `OnSleep` , mas agora está em execução no primeiro plano novamente. O programa pode usar essa oportunidade para atualizar as conexões de Internet (por exemplo).

Uma chamada a `OnStart` ocorrer durante a inicialização do programa. Não é necessário aguardar até que essa chamada de método acesse o `Properties` dicionário porque o conteúdo já foi restaurado quando o `App` Construtor é chamado.

O exemplo de [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) é muito semelhante a **SimplestKeypad** , exceto pelo fato de que o programa usa a `OnSleep` substituição para salvar a entrada do teclado atual e o construtor da página para restaurar esses dados.

> [!NOTE]
> Outra abordagem para salvar as configurações do programa é fornecida pela Xamarin.Essentials classe [Preferences](~/essentials/preferences.md) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 6 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemplos do capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Exemplos do capítulo 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.FormsButton](~/xamarin-forms/user-interface/button.md)
