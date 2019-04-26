---
title: Empacotando aplicativos de desgaste
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 585c276b327a9092bdd13fa633307477017558c5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276767"
---
# <a name="packaging-wear-apps"></a>Empacotando aplicativos de desgaste

Aplicativos do Android Wear são empacotados com um aplicativo Android completo para a distribuição no Google Play. 

## <a name="automatic-packaging"></a>Empacotamento automática

Começando com o Xamarin Android 5.0, seu aplicativo de desgaste está automaticamente em um recurso em seu aplicativo mão quando você cria uma referência de projeto do projeto portátil para o projeto de desgaste. Você pode usar as etapas a seguir para criar essa associação: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Se seu aplicativo de desgaste não ainda faz parte de sua solução de bolso, com o botão direito no nó da solução e selecione **Adicionar > Adicionar projeto existente...** .

2. Navegue até a **. csproj** arquivo do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto de aplicativo de desgaste agora deve estar visível em sua solução portátil.

3. Clique com botão direito do **referências** nó e selecione **adicionar referência**.

4. No **Gerenciador de referências** caixa de diálogo, permitem que seu projeto de desgaste (clique para adicionar uma marca de seleção), em seguida, clique em **Okey**.

5. Alterar o nome do pacote para o seu projeto de desgaste para que corresponda o nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **Propriedades > manifesto do Android**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Se seu aplicativo de desgaste não ainda faz parte de sua solução de bolso, com o botão direito no nó da solução e selecione **Adicionar > Adicionar projeto existente...** .

2. Navegue até a **. csproj** arquivo do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto de aplicativo de desgaste agora deve estar visível em sua solução portátil.

3. Clique com botão direito no nó do projeto portátil na solução e clique em **Editar referências...** .

4. No **Editar referências** caixa de diálogo, permitem que seu projeto de desgaste (clique para adicionar uma marca de seleção), em seguida, clique em **Okey**.

5. Alterar o nome do pacote para o seu projeto de desgaste para que corresponda o nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **opções de projeto > aplicativo Android**).

-----


Observe que você obterá um **XA5211** erro se o nome do pacote do aplicativo Wear não coincide com o nome do pacote do aplicativo portátil. Por exemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corrigir esse erro, altere o nome do pacote do aplicativo Wear para que corresponda o nome do pacote do aplicativo portátil.

Quando você clica em **compilar > compilar tudo**, essa associação dispara automática empacotamento do projeto desgaste ao projeto principal do Handheld (telefone). O aplicativo de desgaste é automaticamente criado e incluído como um recurso no aplicativo portátil.

O assembly que o projeto de aplicativo de desgaste gera não é usado como uma referência de assembly no projeto Handheld (telefone). Em vez disso, o processo de compilação faz o seguinte:

-   Verifica se o pacote de nomes de correspondência. 

-   Gera XML e o adiciona ao projeto portátil para associá-lo com o aplicativo de desgaste. Por exemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Adiciona o aplicativo de desgaste como uma **brutos** recursos ao projeto portátil. 


## <a name="manual-packaging"></a>Empacotamento manual

Você pode escrever aplicativos do Android Wear no xamarin. Android antes da versão 5.0, mas você deve seguir estas instruções de empacotamento manual para distribuir o aplicativo: 

1. Certifique-se de que seu projeto portáteis e projetos Handheld (telefone) têm o mesmo nome de pacote e o número de versão.

2. Criar manualmente o projeto portáteis como uma **versão** build.

3. Adicionar manualmente a versão **. APK** da etapa (2) para o **recursos/bruto** diretório do projeto Handheld (telefone).

4. Adicionar manualmente um novo recurso XML **Resources/xml/wearable_app_desc.xml** no projeto portátil que se refere ao dispositivo portátil **APK** da etapa (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Adicionar manualmente uma `<meta-data />` elemento para o projeto portátil **androidmanifest. XML** `<application>` elemento refere-se para o novo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Consulte também o site de desenvolvedor do Android [packging manual instruções](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

