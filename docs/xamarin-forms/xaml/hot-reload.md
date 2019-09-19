---
title: Hot recarregamento de XAML para Xamarin. Forms
description: Recarregue as alterações no arquivo XAML instantaneamente em seu aplicativo em execução, para que você não precise compilar seu projeto Xamarin. Forms após cada alteração em XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 2a47876b7b53cf557014c772333e651146afe53f
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106006"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Hot recarregamento de XAML para Xamarin. Forms (visualização)

![Versão prévia do recurso](~/media/shared/preview.png)

Os Hot recarregamento de XAML são conectados ao fluxo de trabalho existente para aumentar a produtividade e poupar tempo. Sem o Hot recarregamento de XAML, você precisa compilar e implantar seu aplicativo toda vez que desejar ver uma alteração XAML. Com a recarga a quente, quando você salva o arquivo XAML, as alterações são refletidas em seu aplicativo em execução. Além disso, o estado de navegação e os dados serão mantidos, permitindo que você itere rapidamente na interface do usuário sem perder seu lugar no aplicativo. Portanto, com o Hot recarregamento de XAML, você gastará menos tempo recriando e implantando seus aplicativos para validar as alterações na interface do usuário.

> [!NOTE]
> Se você estiver escrevendo um aplicativo do WPF ou UWP, confira [Hot recarregamento de XAML para UWP e WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Requisitos de sistema

| IDE/estrutura | Versão necessária |
|------|------------------|
|Visual Studio 2019 | 16,3 ou superior
Visual Studio 2019 para Mac | 8,3 ou superior
Xamarin.Forms | 4,1 ou superior

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Usar o Hot recarregamento de XAML para Xamarin. Forms

Nenhuma instalação ou configuração adicional é necessária para usar o Hot recarregamento de XAML. Ele é incorporado ao Visual Studio e pode ser habilitado nas configurações do IDE. Uma vez habilitado, você pode começar a usar o Hot recarregamento de XAML Depurando seu aplicativo em um emulador, simulador ou dispositivo físico. Atualmente, o recarregamento dinâmico de XAML funciona apenas durante a depuração no iOS ou Android.

No Windows, o Hot recarregamento de XAML pode ser habilitado marcando a caixa de seleção **habilitar o WebLoad do xamarin** em **ferramentas** > **Opções** > **xamarin** > **Hot recarregar**.

Em um Mac, o Hot recarregamento XAML pode ser habilitado marcando a caixa de seleção **habilitar o WebLoad do xamarin** em**projetos** > de**preferências** > do **Visual Studio** > a**recarga automática do xamarin**.

## <a name="resilient-reloading"></a>Recarregamento resiliente

Se você fizer uma alteração que o Hot recarregamento de XAML não possa recarregar, ele mostrará uma mensagem de erro usando o IntelliSense. Essas alterações, conhecidas como edições rudes, incluem a digitação incorreta do XAML ou a ligação de um controle a um manipulador de eventos que não existe. Mesmo com uma edição rude, você pode continuar a recarregar sem reiniciar o aplicativo. faça outra alteração em outro lugar no arquivo XAML e clique em salvar. A edição rude não será recarregada, mas suas outras alterações continuarão sendo aplicadas.

## <a name="known-limitations"></a>Limitações conhecidas

- Você não pode adicionar, remover ou renomear arquivos ou pacotes NuGet durante uma sessão de recarga ativa de XAML. Se você adicionar ou remover um arquivo ou pacote NuGet, recompile e reimplante seu aplicativo para continuar usando o Hot recarregamento de XAML.
- Defina o vinculador para **vincular nenhum** para obter a melhor experiência. A configuração do **SDK do link somente** funciona na maior parte do tempo, mas pode falhar em determinados casos.
- A depuração em um iPhone físico exige que o intérprete use o Hot recarregamento de XAML. Adicionar **--intérprete** ao campo de **argumentos adicionais do mTouch** em suas configurações de Build do IOS para usar o Hot recarregamento de XAML.
- Todas as referências criadas por meio da atribuição de um controle a outro campo ou propriedade `x:Name` usando seu valor não serão recarregadas.
- Atualizar a hierarquia visual do aplicativo Shell no **AppShell. XAML** pode causar problemas ao manter o estado do seu aplicativo. Recompile o aplicativo para continuar recarregando.
- O Hot recarregamento de XAML não C# pode recarregar o código, incluindo manipuladores de eventos, controles personalizados, code-behind de página e classes adicionais.

## <a name="migrate-from-the-private-preview"></a>Migrar da versão prévia privada

Se você fazia parte da visualização privada, sua extensão de recarregamento de XAML é atualizada automaticamente quando o Visual Studio for atualizado. Se você optar por não atualizar o Visual Studio, poderá continuar usando a versão atual do Hot recarregamento de XAML, mas não receberá nenhuma atualização adicional por meio do feed de extensão de visualização particular.

## <a name="troubleshooting"></a>Solução de problemas

- Se o Hot recarregamento do XAML falhar ao inicializar:
  - Atualize sua versão do Xamarin. Forms.
  - Verifique se você está na versão mais recente do IDE.
  - Defina as configurações do vinculador do Android ou iOS para **não vincular** nas configurações de Build do projeto.
- Se nada acontecer ao salvar o arquivo XAML, certifique-se de que o Hot recarregamento esteja habilitado no IDE.
- Se você estiver Depurando em um iPhone físico e seu aplicativo ficar sem resposta, verifique se o intérprete está habilitado. Para ativá-lo, adicione **--intérprete** ao campo de **argumentos mTouch adicionais** nas configurações de Build do Ios.

Para relatar um bug, use a ferramenta de comentários no menu **ajuda** > **enviar comentários** > e**um problema** no Windows e a **ajuda** > **relatar um** menu de problemas em um Mac.
