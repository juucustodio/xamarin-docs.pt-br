---
title: Atualizando referências de componentes para NuGet
description: Este documento descreve como substituir suas referências de componente com pacotes do NuGet à prova de obsolescência seus aplicativos, desde que o Store do componente Xamarin foi descontinuado.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 70ca9a73c83bed5233b77a6f7be80a13f04f2bcb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360717"
---
# <a name="updating-component-references-to-nuget"></a>Atualizando referências de componentes para NuGet

> [!IMPORTANT]
> O componente Store foi descontinuado a partir de 15 de maio de 2018 (esse fechamento era originalmente [anunciou](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) em novembro de 2017).
>
> Componentes do Xamarin não são mais suportados no Visual Studio e devem ser substituídos por pacotes do NuGet. Siga as instruções abaixo para remover manualmente referências de componentes de seus projetos.

Consulte estas instruções para adicionar pacotes NuGet na [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) ou [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Uma lista de Xamarin popular [plug-ins e bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md) está disponível para ajudar a encontrar alternativas para componentes que não estão disponíveis como pacotes NuGet.

## <a name="manually-removing-component-references"></a>Remover manualmente referências de componentes

A versão 15.6 do Visual Studio e a versão 7.4 do Visual Studio para Mac não há mais suportam a componentes em seu projeto. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Se você carregar um projeto no Visual Studio, a seguinte caixa de diálogo será exibida, explicando que você deve remover todos os componentes do seu projeto manualmente:

![Caixa de diálogo explicando que um componente foi encontrado no seu projeto e deve ser removido de alerta](component-nuget-images/component-alert-vs.png)

Para remover um componente de seu projeto:

1. Abra o **. csproj** arquivo. Para fazer isso, clique com botão direito no nome do projeto e selecione **descarregar projeto**. 

2. Clique novamente no projeto descarregado e selecione **editar. csproj do {your-project-name}**.

3. Localizar todas as referências no arquivo para `XamarinComponentReference`. Ele deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Remover as referências a `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o `ItemGroup`.

5. Depois que o arquivo foi salvo, clique com botão direito no nome do projeto e selecione **recarregar projeto**.

6. Repita as etapas acima para cada projeto na solução.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Se você carregar um projeto no Visual Studio para Mac, a seguinte caixa de diálogo será exibida, explicando que você deve remover todos os componentes do seu projeto manualmente:

![Caixa de diálogo explicando que um componente foi encontrado no seu projeto e deve ser removido de alerta](component-nuget-images/component-alert.png)

Para remover um componente de seu projeto:

1. Abra o arquivo. csproj. Para fazer isso, clique com botão direito no nome do projeto e selecione **Ferramentas > Editar arquivo**.

2. Localizar todas as referências no arquivo para `XamarinComponentReference`. Ele deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Remover as referências a `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o `ItemGroup`

4. Repita as etapas acima para cada projeto na solução.

-----

> [!WARNING]
> As instruções a seguir só funcionam com versões mais antigas do Visual Studio.
> O **componentes** nó não está mais disponível nas versões atuais do Visual Studio 2017 ou Visual Studio para Mac.

As seções a seguir explicam como atualizar as soluções existentes do Xamarin para alterar referências de componentes para pacotes do NuGet.

- [Componentes que contêm os pacotes do NuGet](#contain)
- [Componentes com substituições de NuGet](#replace)

A maioria dos componentes se encaixam em uma das categorias acima.
Se você estiver usando um componente que não parece ter um equivalente pacote do NuGet, leia as [componentes sem um caminho de migração do NuGet](#require-update) seção abaixo.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componentes que contêm os pacotes do NuGet

Muitos componentes já contêm os pacotes do NuGet e o caminho de migração é simplesmente exclua a referência do componente.

Você pode determinar se o componente já inclui um pacote do NuGet clicando duas vezes no componente da solução:

![Nó de componentes expandido](component-nuget-images/solution-sml.png)

O **pacotes** guia listará todos os pacotes NuGet incluídos no componente:

![Guia pacotes contém NuGet](component-nuget-images/packages-tab-sml.png)

Observe que o **Assemblies** guia estará vazia:

![Guia assemblies está vazio](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Atualizar a solução

Para atualizar sua solução, exclua o **componente** entrada da solução:

![Excluir componente](component-nuget-images/delete-component-sml.png)

O pacote do NuGet permanecerá listado na **pacotes** nó e seu aplicativo serão compilado e executado como de costume. No futuro, as atualizações para esse pacote serão executadas por meio de **Nuget** recurso de atualização:

![Atualizar pacote NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componentes com substituições de NuGet

Se a página de informações do componente **Assemblies** guia contém as entradas, conforme mostrado abaixo, você precisará localizar o pacote do NuGet equivalente manualmente.

![Contém os assemblies](component-nuget-images/assemblies-tab-sml.png)

Observe que o **pacotes** guia provavelmente estará vazia:

![](component-nuget-images/packages-tab-empty-sml.png)

_Ele pode conter dependências do NuGet, mas elas podem ser ignoradas._

Para confirmar uma substituição existe pacote do NuGet, pesquise [NuGet.org](https://www.nuget.org/packages), usando o nome do componente, ou como alternativa, por autor.

Por exemplo, você pode encontrar a popular **sqlite-net-pcl** pacote por meio de pesquisa:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – o nome do produto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – perfil do autor.

### <a name="updating-the-solution"></a>Atualizar a solução

Depois de confirmar, que o componente está disponível no NuGet, siga estas etapas:

#### <a name="delete-the-component"></a>Excluir o componente

Clique com o botão direito no componente da solução e escolha **remover**:

![Remover componente](component-nuget-images/remove-component-sml.png)

Isso excluirá o componente e todas as referências. Isso interromperá o build, até que você adicione o pacote do NuGet equivalente para substituí-lo.

#### <a name="add-the-nuget-package"></a>Adicione o pacote NuGet

1. Clique com botão direito no **pacotes** nó e escolha **adicionar pacotes...** .
2. Procure a substituição do NuGet por nome ou o autor:

  ![](component-nuget-images/nuget-search-sml.png)

3. Pressione **Adicionar pacote**.

O pacote do NuGet será adicionado ao seu projeto, juntamente com todas as dependências.
Isso deve corrigir a compilação. Se a compilação continuar falhando, investigue cada erro para ver se houve diferenças de API entre o componente e o pacote do NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componentes sem um caminho de migração do NuGet

Não se preocupe se você não encontrar imediatamente uma substituição para componentes usados em seu aplicativo. Os componentes existentes continuarão funcionando no Visual Studio 15.5 e o **componentes** nó aparecerá em sua solução como de costume.

Versões futuras do Visual Studio, no entanto, serão _não_ restaurar ou atualizar os componentes.
Isso significa que se você abrir a solução em um novo computador, o componente não ser baixado e instalado; e o autor não será capaz de fornecer as atualizações. Você deve planejar:

* Extraia os assemblies do componente e referenciá-los diretamente em seu projeto.
* Entre em contato com o autor do componente e pergunte sobre planos de migrar para o NuGet.
* Investigue os pacotes do NuGet alternativos ou buscar o código-fonte, se o componente é o código-fonte aberto.

Muitos fornecedores de componentes ainda estão em funcionamento sobre como migrar para o NuGet e outras pessoas (incluindo os produtos disponíveis comercialmente) podem estar investigando opções de entrega alternativos.


## <a name="related-links"></a>Links relacionados
- [Lista de Xamarin Plugins e bibliotecas populares](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar e usar um pacote do NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluindo um pacote do NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
