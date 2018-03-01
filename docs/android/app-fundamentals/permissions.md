---
title: "Permissões em xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: d20b2aa7df17f2000e2de9cb67f091c52989719b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="permissions-in-xamarinandroid"></a>Permissões em xamarin

<a name="overview" />

## <a name="overview"></a>Visão geral

Aplicativos Android executados em seu próprios proteção e segurança motivos não têm acesso a determinados recursos do sistema ou hardware no dispositivo. O usuário deve conceder explicitamente a permissão para o aplicativo antes que ele pode usar esses recursos. Por exemplo, um aplicativo não pode acessar o GPS em um dispositivo sem permissão explícita do usuário. Android lançará um `Java.Lang.SecurityException` se um aplicativo tentar acessar um recurso protegido sem permissão.

As permissões são declaradas no **AndroidManifest.xml** pelo desenvolvedor do aplicativo quando o aplicativo é desenvolvido. Android tem dois fluxos de trabalho diferentes para obter o consentimento do usuário para essas permissões:
 
* Para aplicativos direcionados 5.1 Android (API nível 22) ou inferior, a solicitação de permissão ocorreu quando o aplicativo foi instalado. Se o usuário não conceder as permissões, o aplicativo não será instalado. Depois que o aplicativo é instalado, não é possível revogar as permissões, exceto por desinstalar o aplicativo.
* A partir do Android 6.0 (API nível 23), os usuários foram fornecidos mais controle sobre as permissões; eles podem conceder ou revogar permissões, desde que o aplicativo está instalado no dispositivo. Esta captura de tela mostra as configurações de permissão para o aplicativo de contatos do Google. Ele lista as várias permissões e permite que o usuário habilitar ou desabilitar permissões:

![Tela de exemplo de permissões](permissions-images/01-permissions-check.png) 

Aplicativos do Android devem verificar em tempo de execução para ver se eles têm permissão para acessar um recurso protegido. Se o aplicativo não tiver permissão, em seguida, ele deve fazer solicitações usando as novas APIs fornecidas pelo SDK do Android para o usuário para conceder as permissões. As permissões são divididas em duas categorias:

* **Permissões de normal** &ndash; essas são as permissões que apresentam pequeno risco de segurança para o usuário de segurança ou privacidade. Android 6.0 automaticamente concederá permissões normais no momento da instalação. Consulte a documentação do Android para um [lista completa de permissões normais](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Permissões perigosas** &ndash; em contraste com as permissões normais, permissões perigosas são aqueles que protegem a segurança ou privacidade do usuário. Eles devem ser explicitamente concedida pelo usuário. Enviar ou receber uma mensagem SMS é um exemplo de uma ação que exige uma permissão perigosa.

> [!IMPORTANT]
> A categoria que pertence uma permissão pode ser alterado ao longo do tempo.  É possível que uma permissão que foi categorizada como uma permissão "normal" pode ser escalado no futuro níveis de API para uma permissão perigosa.

Permissões perigosas mais são subdivididas em [ _grupos de permissão_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Um grupo de permissão armazenará as permissões que são logicamente relacionadas. Quando o usuário concede permissão a um membro de um grupo de permissão, o Android automaticamente concede permissão a todos os membros desse grupo. Por exemplo, o [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) grupo de permissão contém tanto o `WRITE_EXTERNAL_STORAGE` e `READ_EXTERNAL_STORAGE` permissões. Se o usuário concede permissão para `READ_EXTERNAL_STORAGE`, em seguida, o `WRITE_EXTERNAL_STORAGE` permissão é concedida automaticamente ao mesmo tempo.

Antes de solicitar uma ou mais permissões, é uma prática recomendada para fornecer uma lógica sobre por que o aplicativo requer a permissão antes de solicitar a permissão. Depois que o usuário compreende a lógica, o aplicativo pode solicitar a permissão do usuário. Compreendendo a lógica, o usuário pode tomar uma decisão informada se desejar conceder a permissão e entender as repercussões se eles não são. 

O fluxo de trabalho inteiro de verificação e solicitando permissões é conhecido como um _permissões de tempo de execução_ Verifique e pode ser resumido no diagrama a seguir: 

[ ![Gráfico de fluxo de verificação de permissão de tempo de execução](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png)

A biblioteca de suporte Android backports algumas das novas APIs para permissões em versões anteriores do Android. Essas APIs backported verificará automaticamente a versão do Android no dispositivo não é necessário executar uma verificação de nível de API cada vez.  

Este documento explicará como adicionar permissões a um aplicativo xamarin e como aplicativos para Android 6.0 (API nível 23) ou superior, deve executar uma verificação de permissão de tempo de execução.


> [!NOTE]
> **Observação:** é possível que as permissões de hardware podem afetar como o aplicativo é filtrado pelo Google Play. Por exemplo, se o aplicativo requer a permissão para a câmera, em seguida, Google Play não mostrará o aplicativo no Google Play Store em um dispositivo que não tem uma câmera instalada.


<a name="requirements" />

## <a name="requirements"></a>Requisitos

É altamente recomendável que incluem projetos xamarin o [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) pacote NuGet. Essa permissão de backport do pacote será APIs específicas para versões mais antigas do Android, fornecendo um comum constantemente interface sem a necessidade de verificar a versão do Android que o aplicativo está em execução.

<a name="requesting_permissions" />

## <a name="requesting-system-permissions"></a>Solicitando permissões do sistema

É a primeira etapa ao trabalhar com permissões de Android declarar que o Android as permissões de arquivo de manifesto. Isso deve ser feito, independentemente do nível de API que o aplicativo está direcionando.

Aplicativos de destino Android 6.0 ou superior não podem assumir que porque o usuário a permissão em algum momento no passado, se a permissão será válida na próxima vez. Um aplicativo que tem como alvo o Android 6.0 sempre deve executar uma verificação de permissão de tempo de execução. Aplicativos para Android 5.1 ou inferior não precisa executar uma verificação de permissão de tempo de execução.

> [!NOTE]
> **Observação:** aplicativos só devem solicitar as permissões que eles exigem.

<a name="declaring_permissions_in_the_manifest" />

### <a name="declaring-permissions-in-the-manifest"></a>Declarando permissões no manifesto

As permissões são adicionadas para o **AndroidManifest.xml** com o `uses-permission` elemento. Por exemplo, se um aplicativo é localizar a posição do dispositivo, ele requer bem e permissões do local do curso. Os dois elementos a seguir são adicionados ao manifesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

É possível declarar as permissões usando o suporte de ferramenta embutido no Visual Studio:

1. Clique duas vezes em **propriedades** no **Solution Explorer** e selecione o **manifesto do Android** guia na janela Propriedades:

    [![Permissões necessárias na guia do manifesto do Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png)

2. Se o aplicativo ainda não tiver um AndroidManifest.xml, clique em **AndroidManifest.xml não encontrado. Clique para adicionar um** conforme mostrado abaixo:

    [![Nenhuma mensagem AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png)

3. Selecione as permissões que seu aplicativo precisa do **as permissões necessárias** Liste e salvar:

    [![Exemplo de permissões de CÂMERA selecionado](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

É possível declarar as permissões usando o suporte de ferramenta embutido no Visual Studio para Mac:

1. Clique duas vezes no projeto no **solução preenchimento** e selecione **opções > compilar > aplicativo Android**:

    [![Seção de permissões necessária mostrada](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png)

2. Clique o **adicionar manifesto do Android** botão se o projeto não tiver um **AndroidManifest.xml**:

    [![Manifesto do Android do projeto está ausente](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png)

3. Selecione as permissões que seu aplicativo precisa do **as permissões necessárias** lista e clique em **Okey**:

    [![Exemplo de permissões de CÂMERA selecionado](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png)
    
-----

Xamarin automaticamente adicionará algumas permissões no momento da compilação para compilações de depuração. Isso tornará o aplicativo mais fácil de depuração. Em particular, duas permissões importantes são `INTERNET` e `READ_EXTERNAL_STORAGE`. Essas permissões automaticamente conjunto não aparecerá para ser habilitada no **as permissões necessárias** lista. Compilações de versão, no entanto, use somente as permissões que são definidas explicitamente no **as permissões necessárias** lista. 

Para aplicativos que se destinam a 5.1 Android (API nível 22) ou inferior, não há nada mais que precisa ser feito. Aplicativos que serão executados no Android 6.0 (API 23 nível 23) ou superior devem prosseguir para a próxima seção sobre como executar verificações de permissão de tempo de execução. 

<a name="run_time_permission_checks" />

### <a name="runtime-permission-checks-in-android-60"></a>Verificações de permissão de tempo de execução no Android 6.0

O `ContextCompat.CheckSelfPermission` método (disponível com a biblioteca de suporte do Android) é usado para verificar se uma permissão específica tiver recebida. Este método retornará um [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum que tem um dos dois valores:

* **`Permission.Granted`** &ndash; Concedeu a permissão especificada.
* **`Permission.Denied`** &ndash; A permissão especificada não recebeu.

Este trecho de código é um exemplo de como verificar a permissão de câmera em uma atividade: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

É uma prática recomendada para informar ao usuário sobre por que uma permissão é necessária para um aplicativo para que uma decisão informada pode ser feita para conceder a permissão. Um exemplo disso seria um aplicativo que usa marcas de localização geográfica e fotos-los. É claro para o usuário que a permissão de câmera é necessária, mas pode não ser criptografado por que o aplicativo também precisa do local do dispositivo. A lógica deve exibir uma mensagem para ajudar o usuário a entender por que a permissão de local é desejável e que a permissão de câmera é necessária.

O `ActivityCompat.ShouldShowRequestPermissionRational` método é usado para determinar se a lógica deve ser mostrada para o usuário. Esse método retornará `true` se a lógica para uma determinada permissão deve ser exibida. Esta captura de tela mostra um exemplo de um Snackbar exibida por um aplicativo que explica por que o aplicativo precisa saber o local do dispositivo:

![Razão para local](permissions-images/07-rationale-snackbar.png) 

Se o usuário concede a permissão, o `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` método deve ser chamado. Esse método requer os seguintes parâmetros:

* **atividade** &ndash; essa é a atividade que está solicitando permissões e deve ser informado pelo Android dos resultados.
* **permissões** &ndash; uma lista das permissões que estão sendo solicitados.
* **requestCode** &ndash; um valor inteiro que é usado para corresponder os resultados da solicitação de permissão para um `RequestPermissions` chamar. Esse valor deve ser maior que zero.

Este trecho de código é um exemplo dos dois métodos que foram abordados. Primeiro, é feita uma verificação para determinar se a lógica de permissão deve ser mostrada. Se a lógica será mostrado, um Snackbar é exibido com a lógica. Se o usuário clica em **Okey** Snackbar, em seguida, o aplicativo solicitará as permissões. Se o usuário não aceitar a lógica, o aplicativo não deve continuar para solicitar permissões. Se a lógica não for exibida, a atividade irá solicitar a permissão:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` pode ser chamado mesmo se o usuário já tem permissão. Não há necessidade de chamadas subsequentes, mas eles fornecem o usuário a oportunidade para confirmar a permissão (ou revogar uma). Quando `RequestPermission` é chamado, controle é transferido para o sistema operacional, que exibe uma interface do usuário para aceitar as permissões:  

![Caixa de diálogo Permssion](permissions-images/08-location-permission-dialog.png)

Depois que o usuário for concluído, Android retornará os resultados para a atividade por meio de um método de retorno de chamada, `OnRequestPermissionResult`. Esse método é uma parte da interface `ActivityCompat.IOnRequestPermissionsResultCallback` que deve ser implementado pela atividade. Essa interface possui um único método, `OnRequestPermissionsResult`, que será invocado pelo Android para informar a atividade de opções do usuário. Se o usuário tem a permissão, o aplicativo pode vá em frente e usar o recurso protegido. Um exemplo de como implementar `OnRequestPermissionResult` é mostrado abaixo: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  

<a name="summary" />

## <a name="summary"></a>Resumo

Este guia discutidos como adicionar e verifique as permissões em um dispositivo Android. As diferenças em como as permissões funcionam entre aplicativos do Android antigos (API nível 23 <) e aplicativos novos do Android (API nível 22 >). Ele discutiu como executar verificações de permissão de tempo de execução no Android 6.0.


## <a name="related-links"></a>Links relacionados

- [Lista de permissões Normal](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Aplicativo de exemplo de permissões de tempo de execução](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Tratamento de permissões em xamarin](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
