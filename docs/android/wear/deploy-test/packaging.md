---
title: Empacotando aplicativos de desgaste
ms.topic: article
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: a3eb5cd5b4202db8c58870c2b2c679b47f79d4aa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="packaging-wear-apps"></a>Empacotando aplicativos de desgaste

Aplicativos do Android desgaste são empacotados com um aplicativo do Android completo para a distribuição no Google Play. 

## <a name="automatic-packaging"></a>Empacotamento automática

Começando com o Xamarin Android 5.0, seu aplicativo desgaste está automaticamente em um recurso em seu aplicativo mão quando você cria uma referência de projeto do projeto portátil para o projeto de desgaste. Você pode usar as etapas a seguir para criar essa associação: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Se seu aplicativo desgaste não ainda faz parte de sua solução de bolso, com o botão direito no nó da solução e selecione **Adicionar > Adicionar projeto existente...** .

2. Navegue até o **. csproj** arquivo do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto do aplicativo desgaste agora deve estar visível em sua solução portátil.

3. Clique com botão direito do **referências** nó e selecione **adicionar referência**.

4. No **Gerenciador de referências** caixa de diálogo, habilite seu projeto de desgaste (clique para adicionar uma marca de seleção), em seguida, clique em **Okey**.

5. Alterar o nome do pacote para o seu projeto de desgaste para que corresponda ao nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **Propriedades > manifesto do Android**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Se seu aplicativo desgaste não ainda faz parte de sua solução de bolso, com o botão direito no nó da solução e selecione **Adicionar > Adicionar projeto existente...** .

2. Navegue até o **. csproj** arquivo do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto do aplicativo desgaste agora deve estar visível em sua solução portátil.

3. Clique com botão direito no nó do projeto portátil a solução e clique em **Editar referências...** .

4. No **Editar referências** caixa de diálogo, habilite seu projeto de desgaste (clique para adicionar uma marca de seleção), em seguida, clique em **Okey**.

5. Alterar o nome do pacote para o seu projeto de desgaste para que corresponda ao nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **opções de projeto > aplicativo Android**).

-----


Observe que você obterá um **XA5211** erro se o nome do pacote do aplicativo desgaste não coincide com o nome do pacote do aplicativo portátil. Por exemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corrigir esse erro, altere o nome do pacote do aplicativo desgaste para que corresponda ao nome do pacote do aplicativo portátil.

Quando você clica em **Construir > compilar tudo**, essa associação dispara empacotamento automática do projeto desgaste no projeto principal Handheld (Phone). O aplicativo de desgaste é automaticamente criado e incluído como um recurso no aplicativo portátil.

O assembly que o projeto de aplicativo de desgaste gera não é usado como uma referência de assembly no projeto Handheld (Phone). Em vez disso, o processo de compilação faz o seguinte:

-   Verifica se o pacote nomes correspondentes. 

-   Gera o XML e o adiciona ao projeto portátil para associá-lo com o aplicativo de desgaste. Por exemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Adiciona o aplicativo de desgaste como um **bruto** recursos para o projeto portátil. 


## <a name="manual-packaging"></a>Empacotamento manual

Você pode escrever aplicativos Android desempenham em xamarin antes da versão 5.0, mas você deve seguir estas instruções de empacotamento manual para distribuir o aplicativo: 

1. Certifique-se de que o seu projeto vestir e projetos Handheld (Phone) têm o mesmo nome de pacote e o número de versão.

2. Criar manualmente o projeto de vestir como um **versão** de compilação.

3. Adicione manualmente a versão **. APK** da etapa (2) para o **recursos/raw** diretório do projeto Handheld (Phone).

4. Adicionar manualmente um novo recurso XML **Resources/xml/wearable_app_desc.xml** no projeto portátil que se refere ao vestir **APK** da etapa (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Adicionar manualmente uma `<meta-data />` elemento para o projeto portátil **AndroidManifest.xml** `<application>` elemento refere-se para o novo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Consulte também o site de desenvolvedor Android [packging manual instruções](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

