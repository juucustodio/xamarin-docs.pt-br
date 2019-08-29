---
title: Permissões no Xamarin. Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: ef73b8e1cf9747c9ba426894f37aab620ac0095f
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119148"
---
# <a name="permissions-in-xamarinandroid"></a>Permissões no Xamarin. Android


## <a name="overview"></a>Visão geral

Os aplicativos Android são executados em sua própria área restrita e, por motivos de segurança, não têm acesso a determinados recursos do sistema ou hardware no dispositivo. O usuário deve conceder explicitamente permissão ao aplicativo para poder usar esses recursos. Por exemplo, um aplicativo não pode acessar o GPS em um dispositivo sem permissão explícita do usuário. O Android emitirá `Java.Lang.SecurityException` um se um aplicativo tentar acessar um recurso protegido sem permissão.

As permissões são declaradas no **AndroidManifest. xml** pelo desenvolvedor do aplicativo quando o aplicativo é desenvolvido. O Android tem dois fluxos de trabalho diferentes para obter o consentimento do usuário para essas permissões:
 
- Para aplicativos direcionados para Android 5,1 (API nível 22) ou inferior, a solicitação de permissão ocorreu quando o aplicativo foi instalado. Se o usuário não tiver concedido as permissões, o aplicativo não será instalado. Depois que o aplicativo é instalado, não é possível revogar as permissões, exceto desinstalando o aplicativo.
- A partir do Android 6,0 (nível 23 da API), os usuários receberam mais controle sobre as permissões; Eles podem conceder ou revogar permissões, desde que o aplicativo esteja instalado no dispositivo. Esta captura de tela mostra as configurações de permissão para o aplicativo Google Contacts. Ele lista as várias permissões e permite que o usuário habilite ou desabilite as permissões:

![Tela de permissões de exemplo](permissions-images/01-permissions-check.png) 

Os aplicativos Android devem verificar em tempo de execução para ver se eles têm permissão para acessar um recurso protegido. Se o aplicativo não tiver permissão, ele deverá fazer solicitações usando as novas APIs fornecidas pelo SDK do Android para que o usuário conceda as permissões. As permissões são divididas em duas categorias:

- **Permissões normais** &ndash; Essas são permissões que representam pouco risco de segurança à segurança ou privacidade do usuário. O Android 6,0 concederá automaticamente permissões normais no momento da instalação. Consulte a documentação do Android para obter uma [lista completa das permissões normais](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
- **Permissões perigosas** &ndash; Ao contrário das permissões normais, as permissões perigosas são aquelas que protegem a segurança ou a privacidade do usuário. Eles devem ser explicitamente concedidos pelo usuário. Enviar ou receber uma mensagem SMS é um exemplo de uma ação que requer uma permissão perigosa.

> [!IMPORTANT]
> A categoria à qual uma permissão pertence pode mudar ao longo do tempo.  É possível que uma permissão que foi categorizada como uma permissão "normal" possa ser elevada em níveis futuros de API para uma permissão perigosa.

Permissões perigosas são mais subdivididas em [_grupos de permissão_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Um grupo de permissões terá permissões que estão logicamente relacionadas. Quando o usuário concede permissão a um membro de um grupo de permissões, o Android concede automaticamente permissão a todos os membros desse grupo. Por exemplo, o [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) grupo de permissões mantém `WRITE_EXTERNAL_STORAGE` as permissões `READ_EXTERNAL_STORAGE` e. Se o usuário conceder permissão para `READ_EXTERNAL_STORAGE`, a `WRITE_EXTERNAL_STORAGE` permissão será concedida automaticamente ao mesmo tempo.

Antes de solicitar uma ou mais permissões, é uma prática recomendada fornecer uma lógica para o motivo pelo qual o aplicativo requer a permissão antes de solicitar a permissão. Depois que o usuário entender a lógica, o aplicativo poderá solicitar permissão do usuário. Ao compreender a lógica, o usuário poderá tomar uma decisão informada se quiser conceder a permissão e entender as repercussões se elas não tiverem. 

Todo o fluxo de trabalho de verificação e solicitação de permissões é conhecido como verificação de _permissões em tempo de execução_ e pode ser resumido no diagrama a seguir: 

[![Gráfico de fluxo de verificação de permissão em tempo de execução](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

A biblioteca de suporte do Android reporta algumas das novas APIs para obter permissões para versões mais antigas do Android. Essas APIs reportadas verificarão automaticamente a versão do Android no dispositivo, de modo que não é necessário executar uma verificação de nível de API a cada vez.  

Este documento explicará como adicionar permissões a um aplicativo Xamarin. Android e como os aplicativos destinados ao Android 6,0 (API nível 23) ou superior devem executar uma verificação de permissão em tempo de execução.


> [!NOTE]
> É possível que as permissões para hardware possam afetar a forma como o aplicativo é filtrado por Google Play. Por exemplo, se o aplicativo exigir permissão para a câmera, Google Play não mostrará o aplicativo no Google Play Store em um dispositivo que não tem uma câmera instalada.


<a name="requirements" />

## <a name="requirements"></a>Requisitos

É altamente recomendável que os projetos Xamarin. Android incluam o pacote NuGet [xamarin. Android. support. compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) . Esse pacote irá backport as APIs específicas de permissão para versões mais antigas do Android, fornecendo uma interface comum sem a necessidade de verificar constantemente a versão do Android em que o aplicativo está sendo executado.


## <a name="requesting-system-permissions"></a>Solicitando permissões do sistema

A primeira etapa ao trabalhar com permissões do Android é declarar as permissões no arquivo de manifesto do Android. Isso deve ser feito independentemente do nível da API que o aplicativo é voltados.

Os aplicativos destinados ao Android 6,0 ou superior não podem pressupor que, como o usuário concedeu permissão em algum momento no passado, que a permissão será válida na próxima vez. Um aplicativo que tem como alvo o Android 6,0 sempre deve executar uma verificação de permissão de tempo de execução. Os aplicativos que se destinam ao Android 5,1 ou inferior não precisam executar uma verificação de permissão em tempo de execução.

> [!NOTE]
> Os aplicativos devem solicitar apenas as permissões que eles necessitam.


### <a name="declaring-permissions-in-the-manifest"></a>Declarando permissões no manifesto

As permissões são adicionadas ao **AndroidManifest. xml** com `uses-permission` o elemento. Por exemplo, se um aplicativo for para localizar a posição do dispositivo, ele exigirá permissões de local bem e de curso. Os dois elementos a seguir são adicionados ao manifesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

É possível declarar as permissões usando o suporte a ferramentas interno do Visual Studio:

1. Clique duas vezes em **Propriedades** na **Gerenciador de soluções** e selecione a guia **manifesto do Android** na janela Propriedades:

    [![Permissões necessárias na guia manifesto do Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Se o aplicativo ainda não tiver um AndroidManifest. xml, clique em **nenhum AndroidManifest. XML encontrado. Clique para adicionar um** , conforme mostrado abaixo:

    [![Nenhuma mensagem AndroidManifest. xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Selecione as permissões que seu aplicativo precisa da lista de **permissões necessárias** e salve:

    [![Exemplo de permissões de câmera selecionadas](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

É possível declarar as permissões usando o suporte de ferramenta interno do Visual Studio para Mac:

1. Clique duas vezes no projeto no **painel de soluções** e selecione **opções > compilar > aplicativo Android**:

    [![Seção de permissões necessárias mostrada](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Clique no botão **Adicionar manifesto do Android** se o projeto ainda não tiver um **AndroidManifest. xml**:

    [![O manifesto do Android do projeto está ausente](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Selecione as permissões de que seu aplicativo precisa da lista de **permissões necessárias** e clique em **OK**:

    [![Exemplo de permissões de câmera selecionadas](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

O Xamarin. Android adicionará automaticamente algumas permissões no momento da compilação para Depurar compilações. Isso facilitará a depuração do aplicativo. Em particular, duas permissões notáveis `INTERNET` são `READ_EXTERNAL_STORAGE`e. Essas permissões definidas automaticamente não parecerão estar habilitadas na lista de **permissões necessárias** . As compilações de versão, no entanto, usam apenas as permissões definidas explicitamente na lista de **permissões necessárias** . 

Para aplicativos direcionados para Android 5.1 (API nível 22) ou inferior, não há nada mais que precise ser feito. Os aplicativos que serão executados no Android 6,0 (API 23 nível 23) ou superior devem passar para a próxima seção sobre como executar verificações de permissão de tempo de execução. 


### <a name="runtime-permission-checks-in-android-60"></a>Verificações de permissão em tempo de execução no Android 6,0

O `ContextCompat.CheckSelfPermission` método (disponível com a biblioteca de suporte do Android) é usado para verificar se uma permissão específica foi concedida. Esse método retornará um [`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission) enum que tem um dos dois valores:

- **`Permission.Granted`** &ndash; A permissão especificada foi concedida.
- **`Permission.Denied`** &ndash; A permissão especificada não foi concedida.

Este trecho de código é um exemplo de como verificar a permissão da câmera em uma atividade: 

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

É uma prática recomendada informar o usuário sobre por que uma permissão é necessária para um aplicativo para que uma decisão informada possa ser feita para conceder a permissão. Um exemplo disso seria um aplicativo que usa fotos e marcas geográficas. É claro para o usuário que a permissão de câmera é necessária, mas pode não estar claro por que o aplicativo também precisa do local do dispositivo. A lógica deve exibir uma mensagem para ajudar o usuário a entender por que a permissão de localização é desejável e que a permissão de câmera é necessária.

O `ActivityCompat.ShouldShowRequestPermissionRationale` método é usado para determinar se a lógica deve ser mostrada para o usuário. Esse método retornará `true` se a lógica de uma determinada permissão deve ser exibida. Esta captura de tela mostra um exemplo de um snackbar exibido por um aplicativo que explica por que o aplicativo precisa saber o local do dispositivo:

![Lógica para o local](permissions-images/07-rationale-snackbar.png) 

Se o usuário conceder a permissão, o `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` método deverá ser chamado. Esse método requer os seguintes parâmetros:

- **atividade** do &ndash; Essa é a atividade que está solicitando as permissões e deve ser informada pelo Android dos resultados.
- **permissões** do &ndash; Uma lista das permissões que estão sendo solicitadas.
- **requestCode** Um valor inteiro que é usado para corresponder os resultados da solicitação de permissão a uma `RequestPermissions` chamada. &ndash; Esse valor deve ser maior que zero.

Esse trecho de código é um exemplo dos dois métodos que foram discutidos. Primeiro, é feita uma verificação para determinar se a lógica de permissão deve ser mostrada. Se a lógica for mostrada, um snackbar será exibido com a lógica. Se o usuário clicar em **OK** no snackbar, o aplicativo solicitará as permissões. Se o usuário não aceitar a lógica, o aplicativo não deverá continuar solicitando permissões. Se a lógica não for mostrada, a atividade solicitará a permissão:

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

`RequestPermission`pode ser chamado mesmo que o usuário já tenha concedido a permissão. As chamadas subsequentes não são necessárias, mas fornecem ao usuário a oportunidade de confirmar (ou revogar) a permissão. Quando `RequestPermission` é chamado, o controle é enviado para o sistema operacional, que exibirá uma interface do usuário para aceitar as permissões:  

![Caixa de diálogo Permssion](permissions-images/08-location-permission-dialog.png)

Depois que o usuário for concluído, o Android retornará os resultados para a atividade por meio de um `OnRequestPermissionResult`método de retorno de chamada,. Esse método é uma parte da interface `ActivityCompat.IOnRequestPermissionsResultCallback` que deve ser implementada pela atividade. Essa interface tem um único método, `OnRequestPermissionsResult`, que será invocado pelo Android para informar a atividade das opções do usuário. Se o usuário tiver concedido a permissão, o aplicativo poderá continuar e usar o recurso protegido. Um exemplo de como implementar `OnRequestPermissionResult` é mostrado abaixo: 

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
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
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


## <a name="summary"></a>Resumo

Este guia abordou como adicionar e verificar permissões em um dispositivo Android. As diferenças em como as permissões funcionam entre aplicativos Android antigos (nível de API < 23) e novos aplicativos Android (nível de API > 22). Ele abordou como executar verificações de permissão em tempo de execução no Android 6,0.


## <a name="related-links"></a>Links relacionados

- [Lista de permissões normais](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Aplicativo de exemplo de permissões de tempo de execução](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Manipulando permissões no Xamarin. Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
