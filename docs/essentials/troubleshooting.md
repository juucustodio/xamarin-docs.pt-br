---
Título: ' ' Xamarin.Essentials : solução de problemas ' ' Descrição: ' este documento descreve como solucionar problemas encontrados ao desenvolver com a Xamarin.Essentials biblioteca. '
MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Solução de problemas

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Erro: conflito de versão detectado para Xamarin.Android.Support.Compat

O erro a seguir pode ocorrer ao atualizar pacotes do NuGet (ou adicionar um novo pacote) com umXamarin.Forms
projeto que usa Xamarin.Essentials :

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue.
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3)
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

O problema são dependências sem correspondência para os dois NuGets. Isso pode ser resolvido adicionando manualmente uma versão específica da dependência (nesse caso, **Xamarin.Android.Support.Compat**) que pode ser compatível com ambas.

Para fazer isso, adicione o NuGet que é a origem do conflito manualmente e use a lista **Versão** para selecionar uma versão específica. Atualmente, a versão 28.0.0.3 do Xamarin. Android. support. compat & Xamarin. Android. support. Core. util NuGet resolverá esse erro.

Confira [esta postagem no blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obter mais informações e um vídeo sobre como resolver o problema.

Se houver algum problema ou encontrar um bug, informe-o no [ Xamarin.Essentials repositório do GitHub](https://github.com/xamarin/Essentials).
