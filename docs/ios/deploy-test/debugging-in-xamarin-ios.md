---
title: Depuração
description: Aplicativos Xamarin.iOS podem ser depurados com o depurador interno no Visual Studio para Mac ou no Visual Studio.
ms.topic: article
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a449d692a2761f59135930d40ad3708d6ef2e9b9
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="debugging"></a>Depuração

_Aplicativos Xamarin.iOS podem ser depurados com o depurador interno no Visual Studio para Mac ou no Visual Studio._

Use o suporte à depuração nativo do Visual Studio para Mac para depurar C# e outro código de linguagens gerenciadas e use [LLDB](http://lldb.llvm.org/tutorial.html) quando precisar depurar código C, C++ ou Objective C que possa estar vinculando a seu projeto Xamarin.iOS.


> [!NOTE]
> Ao compilar aplicativos no modo de Depuração, o Xamarin.iOS gerará aplicativos muito maiores e mais lentos, uma vez que cada linha de código deve ser instrumentada. Antes de liberar, faça um build de Liberação.

O depurador do Xamarin.iOS é integrado ao IDE e permite que os desenvolvedores depurem aplicativos Xamarin.iOS criados com qualquer uma das linguagens gerenciadas com suporte no Xamarin.iOS no simulador e no dispositivo.

O depurador Xamarin.iOS usa o [Depurador Flexível Mono](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/), o que significa que o código gerado e o tempo de execução Mono cooperam com o IDE para proporcionar uma experiência de depuração. Isso é diferente de depuradores rígidos, como LLDB ou MDB, que controlam um programa sem o conhecimento ou a cooperação do programa depurado.

## <a name="setting-breakpoints"></a>Configuração de pontos de interrupção

Quando estiver pronto para iniciar a depuração de seu aplicativo, a primeira etapa é [definir pontos de interrupção em seu aplicativo](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/). Isso é feito clicando na área de margem do editor, ao lado do número de linha de código em que você deseja interromper:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](debugging-in-xamarin-ios-images/debugging1.png "Configuração de Pontos de interrupção")](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](debugging-in-xamarin-ios-images/debugging1a.png "Configuração de Pontos de interrupção")](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

Você pode exibir todos os pontos de interrupção definidos em seu código indo para o **painel de Pontos de Interrupção**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](debugging-in-xamarin-ios-images/image0a.png "O painel de Pontos de interrupção")](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 Se o painel de Pontos de Interrupção não aparecer automaticamente, você poderá torná-lo visível selecionando _Exibir > Depurar Windows > Pontos de Interrupção_
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](debugging-in-xamarin-ios-images/image0.png "O painel de Pontos de interrupção")](debugging-in-xamarin-ios-images/image0.png#lightbox)

 Se o painel Pontos de Interrupção não aparecer automaticamente, você poderá torná-lo visível selecionando _Depurar > Windows > Pontos de Interrupção_
 
-----

Antes de iniciar a depuração de qualquer aplicativo, sempre garanta que a configuração esteja definida como **Depurar**, uma vez que isso contém um conjunto de ferramentas útil para dar suporte à depuração, como pontos de interrupção, uso de visualizadores de dados e exibição da pilha de chamadas:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](debugging-in-xamarin-ios-images/debugging7.png "Depurando no simulador")](debugging-in-xamarin-ios-images/debugging7.png#lightbox)
[ ![](debugging-in-xamarin-ios-images/debugging7a.png "Depurando em um dispositivo físico")](debugging-in-xamarin-ios-images/debugging7a.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](debugging-in-xamarin-ios-images/debugging7c.png "Depurando no simulador")](debugging-in-xamarin-ios-images/debugging7c.png#lightbox)
[ ![](debugging-in-xamarin-ios-images/debugging7d.png "Depurando em um dispositivo físico")](debugging-in-xamarin-ios-images/debugging7d.png#lightbox)

-----

## <a name="start-debugging"></a>Iniciar a depuração
Para iniciar a depuração, selecione o dispositivo de destino ou semelhante no IDE:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](debugging-in-xamarin-ios-images/debugging7b.png "Selecionar o dispositivo de destino")](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](debugging-in-xamarin-ios-images/debugging7e.png "Selecionar o dispositivo de destino")](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----



Em seguida, implante seu aplicativo pressionando o botão **Executar**.

Quando você atingir um ponto de interrupção, o código será realçado em amarelo:

[![](debugging-in-xamarin-ios-images/image2.png "O código será realçado em amarelo")](debugging-in-xamarin-ios-images/image2.png#lightbox)

Ferramentas de depuração, como inspecionar os valores de objetos, podem ser usadas neste ponto para obter mais informações sobre o que está acontecendo em seu código:

[![](debugging-in-xamarin-ios-images/image3.png "Exibindo um valor de cor")](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>Pontos de interrupção condicionais

Você também pode definir regras para determinar as circunstâncias sob as quais um ponto de interrupção deve ocorrer; isso é conhecido como adicionar um *ponto de interrupção condicional*.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para definir um ponto de interrupção condicional, acesse a **janela Propriedades do Ponto de Interrupção**, o que pode ser feito de duas maneiras:


- Para adicionar um novo ponto de interrupção condicional, clique com o botão direito do mouse na margem do editor, à esquerda do número de linha para o código para o qual deseja definir um ponto de interrupção e selecione o novo ponto de interrupção:

    [![](debugging-in-xamarin-ios-images/image4.png "Selecionar Novo ponto de interrupção")](debugging-in-xamarin-ios-images/image4.png#lightbox)

- Para adicionar uma condição a um ponto de interrupção existente, clique com o botão direito do mouse no ponto de interrupção e selecione **Propriedades de Ponto de Interrupção** ou, no **painel Pontos de Interrupção**, selecione o botão de propriedades ilustrado abaixo:

    [![](debugging-in-xamarin-ios-images/image5.png "O painel de Pontos de interrupção")](debugging-in-xamarin-ios-images/image5.png#lightbox)


Então você pode inserir a condição sob a qual você deseja que o ponto de interrupção ocorra:

[![](debugging-in-xamarin-ios-images/image6.png "Inserir a condição para o Ponto de interrupção ocorrer")](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para definir um ponto de interrupção condicional no Visual Studio 2015, primeiro [defina um ponto de interrupção regular](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/). Clique com o botão direito do mouse no ponto de interrupção para exibir seu menu de contexto:

 [![](debugging-in-xamarin-ios-images/image4vs.png "O menu de contexto do ponto de interrupção")](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

Selecione **Condições…** para exibir o menu _Configurações do Ponto de Interrupção_:

 [![](debugging-in-xamarin-ios-images/image6vs.png "O menu Configurações do Ponto de interrupção")](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

Aqui, você pode inserir as condições sob as quais deseja que o ponto de interrupção ocorra

Para saber mais sobre como usar as condições de ponto de interrupção em versões anteriores do Visual Studio, consulte a [documentação do Visual Studio](https://docs.microsoft.com/visualstudio/debugger/using-breakpoints) sobre esse tópico.

-----

## <a name="navigating-through-code"></a>Navegação no código

Quando um ponto de interrupção for atingido, as ferramentas de depuração permitirão assumir o controle da execução do programa. O IDE exibirá quatro botões, permitindo que você execute e percorra o código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac, eles terão a seguinte aparência:

 [![](debugging-in-xamarin-ios-images/image7.png "As ferramentas de Depuração permitem que o desenvolvedor tenha o controle da execução do programa")](debugging-in-xamarin-ios-images/image7.png#lightbox)

Elas são:

- **Executar/Parar** – isso iniciará/interromperá a execução do código até o próximo ponto de interrupção.
- **Passar** – isso executará a próxima linha de código. Se a próxima linha for uma chamada de função, passar executará a função e parará na próxima linha de código _depois_ da função.
- **Intervir** – isso também executará a próxima linha de código. Se a próxima linha é uma chamada de função, Intervir parará na primeira linha da função, permitindo que você continue a depuração da função linha a linha. Se a próxima linha não for uma função, ela se comportará como Passar.
- **Sair** – isso retornará para a linha na qual a função atual foi chamada.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, eles terão a seguinte aparência:

[![](debugging-in-xamarin-ios-images/image7vs.png "As ferramentas de Depuração permitem que o desenvolvedor tenha o controle da execução do programa")](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

Elas são:

- **Executar/Parar** – isso iniciará/interromperá a execução do código até o próximo ponto de interrupção.
- **Passar (F11)** – isso executará a próxima linha de código. Se a próxima linha for uma chamada de função, passar executará a função e parará na próxima linha de código _depois_ da função.
- **Intervir (F10)** – isso também executará a próxima linha de código. Se a próxima linha é uma chamada de função, Intervir parará na primeira linha da função, permitindo que você continue a depuração da função linha a linha. Se a próxima linha não for uma função, ela se comportará como Passar.
- **Sair (Shift + F11)** – isso retornará para a linha na qual a função atual foi chamada.

Para obter uma documentação mais detalhada sobre Depuração, confira [Navegar pelo Código com o Depurador do Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/navigating-through-code-with-the-debugger).

-----

### <a name="breakpoints"></a>Pontos de interrupção

É importante destacar que o iOS fornece aos aplicativos apenas alguns segundos (10) para a inicialização e conclusão do método `FinishedLaunching` no delegado do Aplicativo. Se o aplicativo não concluir esse método em 10 segundos, o iOS finalizará o processo.

Isso significa que é quase impossível definir pontos de interrupção no código de inicialização do programa. Se você quiser depurar seu código de inicialização, atrase parte da inicialização e coloque-a em um método invocado por temporizador ou em alguma outra forma de método de retorno de chamada que seja executado depois da conclusão de FinishedLaunching.

## <a name="device-diagnostics"></a>Diagnóstico do dispositivo

Se houver um erro ao configurar o depurador, você poderá habilitar o diagnóstico detalhado adicionando "-v -v -v" aos argumentos adicionais mtouch em suas Opções de Projeto. Isso imprimirá informações de erro detalhadas no console do dispositivo.

 <a name="WiFi_Debugging" />

## <a name="wireless-debugging"></a>Depuração Sem Fio

O padrão no Xamarin.iOS é depurar seu aplicativo em dispositivos pela conexão USB. Às vezes, o dispositivo USB poderá ser necessário testar a conexão/desconexão do cabo para desenvolver aplicativos alimentados por ExternalAccessory. Nesses casos, você pode usar a depuração pela rede sem fio.

Para mais informações sobre Depuração e Implantação Sem Fio, consulte o guia [Implantação Sem Fio](~/ios/deploy-test/wireless-deployment.md).

<a name="Technical_Details" />

## <a name="technical-details"></a>Detalhes técnicos

O Xamarin.iOS usa o novo depurador flexível Mono. Diferente do depurador Mono padrão, que é um programa que controla um processo separado usando interfaces de sistema operacional, o depurador flexível funciona fazendo o tempo de execução Mono expor a funcionalidade de depuração por meio de um protocolo de transmissão.

Na inicialização, um aplicativo a ser depurados contata o depurador e o depurador começa a operar. No Xamarin.iOS para Visual Studio, o Agente Mac do Xamarin atua como intermediário entre o aplicativo (Visual Studio) e o depurador.

Esse depurador flexível requer um esquema de depuração cooperativo quando está em execução no dispositivo. Isso significa que seus builds binários ao depurar serão maiores, uma vez que o código é instrumentado para conter código extra em cada ponto de sequência para dar suporte à depuração.

<a name="Accessing_the_Console" />


## <a name="accessing-the-console"></a>Acesso ao console

Logs de falha e a saída da classe Console serão enviados ao console do iPhone. Você pode acessar esse console com Xcode usando o “Organizador” e selecionando o dispositivo no organizador.

Como alternativa, se não desejar iniciar o Xcode, você poderá usar o [Utilitário de Configuração do iPhone](http://www.apple.com/support/iphone/enterprise/) da Apple para acessar diretamente o console. Isso tem benefício adicional de poder acessar os logs do console usando um computador Windows se você estiver depurando um problema em campo.

Para usuários do Visual Studio, há alguns logs disponíveis na janela de saída, mas você deve mudar para o Mac para logs mais completos e detalhados.

-----

<a name="Debugging_Mono's_Class_Libraries" />


## <a name="debugging-monos-class-libraries"></a>Depuração de bibliotecas de classes Mono

O Xamarin.iOS é enviado com o código-fonte para bibliotecas de classes Mono e você pode usar essa etapa única do depurador para ver tudo está funcionando nos bastidores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Como esse recurso consome mais memória durante a depuração, ele está desligado por padrão.


Para habilitar esse recurso, verifique se a opção **Depurar somente o código do projeto; não intervir no código do framework** está desmarcada no menu _Visual Studio para Mac > Preferências > Depurador_, conforme ilustrado abaixo:

[![](debugging-in-xamarin-ios-images/debugging6.png "Depurando bibliotecas de classes do Mono")](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para depurar as bibliotecas de classe no Visual Studio, desabilite **Apenas Meu Código** no menu _Depurar > Opções_. No nó _Depuração > Geral_, desmarque a caixa de seleção **Habilitar Apenas Meu Código**:

[![](debugging-in-xamarin-ios-images/debugging6vs.png "Depurando bibliotecas de classes do Mono")](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

Depois de fazer isso, você pode iniciar o aplicativo e intervir em qualquer uma das bibliotecas de classes centrais do Mono.


## <a name="related-links"></a>Links relacionados

- [Depuração com Xamarin](/visualstudio/mac/debugging/)
- [Visualizações de dados](/visualstudio/mac/data-visualizations/)
- [Definir um Ponto de Interrupção](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/)
- [Percorrer o Código](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/)
- [Informações de Saída para a Janela de Log](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/)
