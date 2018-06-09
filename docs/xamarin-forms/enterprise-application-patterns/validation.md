---
title: Validação em aplicativos da empresa
description: Este capítulo explica como o aplicativo móvel eShopOnContainers executa a validação de entrada do usuário. Isso inclui especificar regras de validação, disparar a validação e exibir erros de validação.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6a7f244b78d5b48dd219f59f1191993d62663bbf
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243171"
---
# <a name="validation-in-enterprise-apps"></a>Validação em aplicativos da empresa

Qualquer aplicativo que aceita a entrada de usuários deve garantir que a entrada é válida. Um aplicativo poderia, por exemplo, verificar para a entrada que contém somente caracteres em um intervalo específico, é de um determinado comprimento ou corresponde a um formato específico. Sem validação, um usuário pode fornecer dados que faz com que o aplicativo falhe. Validação impõe regras de negócio e impede que um invasor insira dados mal-intencionados.

No contexto do modelo do modelo-ViewModel (MVVM) padrão, um modelo de exibição ou modelo geralmente precisará executar a validação de dados e sinalizar erros de validação para o modo de exibição para que o usuário poderá corrigi-los. O aplicativo móvel eShopOnContainers executa a validação de cliente síncrona de propriedades do modelo de exibição e notifica o usuário de quaisquer erros de validação, realçando o controle que contém os dados inválidos e exibindo mensagens de erro que informam o usuário do motivo pelo qual os dados são inválidos. Figura 6-1 mostra as classes envolvidas ao executar a validação no aplicativo móvel do eShopOnContainers.

[![](validation-images/validation.png "Classes de validação no aplicativo móvel eShopOnContainers")](validation-images/validation-large.png#lightbox "classes de validação no aplicativo móvel eShopOnContainers")

**Figura 6-1**: classes de validação no aplicativo móvel eShopOnContainers

Exibir as propriedades de modelo que exigem a validação são do tipo `ValidatableObject<T>`e cada `ValidatableObject<T>` instância tem regras de validação adicionadas ao seu `Validations` propriedade. Validação é chamada a partir do modelo de exibição chamando o `Validate` método o `ValidatableObject<T>` instância, que recupera a validação de regras e executa-los contra o `ValidatableObject<T>` `Value` propriedade. Erros de validação são colocados no `Errors` propriedade do `ValidatableObject<T>` instância e o `IsValid` propriedade do `ValidatableObject<T>` instância é atualizada para indicar se a validação foi bem-sucedida ou falha.

Notificação de alteração de propriedade é fornecida pelo `ExtendedBindableObject` classe e, portanto uma [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle pode vincular o `IsValid` propriedade `ValidatableObject<T>` instância na classe de modelo de exibição sejam notificados ou não os dados inseridos são válidos.

## <a name="specifying-validation-rules"></a>Especificar regras de validação

Regras de validação são especificadas, criando uma classe que deriva de `IValidationRule<T>` interface, que é mostrado no exemplo de código a seguir:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Essa interface Especifica que uma classe de regra de validação deve fornecer um `boolean` `Check` método que é usado para executar a validação necessária, e um `ValidationMessage` propriedade cujo valor é a mensagem de erro de validação que será exibida se Falha de validação.

O seguinte exemplo de código mostra o `IsNotNullOrEmptyRule<T>` regra de validação, que é usada para executar a validação do nome de usuário e senha inserida pelo usuário no `LoginView` ao usar os serviços de simulação no aplicativo móvel do eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

O `Check` método retorna um `boolean` que indica se o argumento de valor é `null`, vazio ou consiste apenas em caracteres de espaço em branco.

Embora não usado pelo aplicativo móvel eShopOnContainers, o exemplo de código a seguir mostra uma regra de validação para validar endereços de email:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

O `Check` método retorna um `boolean` que indica se o argumento de valor é um endereço de email válido. Isso é conseguido por meio de pesquisa o argumento de valor para a primeira ocorrência do padrão de expressão regular especificada no `Regex` construtor. Se o padrão de expressão regular foi encontrada na cadeia de entrada pode ser determinada verificando o valor da `Match` do objeto `Success` propriedade.

> [!NOTE]
> Validação de propriedade às vezes pode envolver propriedades dependentes. Um exemplo de propriedades dependentes é quando o conjunto de valores válidos para A propriedade depende do valor específico que foi definido na propriedade B. Para verificar se o valor da propriedade um é um dos valores permitidos envolve a recuperação do valor da propriedade B. Além disso, quando o valor da propriedade B é alterado, a propriedade um precisaria ser revalidado.

## <a name="adding-validation-rules-to-a-property"></a>Adicionando regras de validação para uma propriedade

No aplicativo móvel do eShopOnContainers, exibir propriedades de modelo que exigem validação são declaradas como sendo do tipo `ValidatableObject<T>`, onde `T` é o tipo de dados a ser validado. O exemplo de código a seguir mostra um exemplo de duas propriedades:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para a validação ocorrer, as regras de validação devem ser adicionadas para o `Validations` coleta de cada `ValidatableObject<T>` instância, conforme demonstrado no exemplo de código a seguir:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Este método adiciona o `IsNotNullOrEmptyRule<T>` regra de validação para o `Validations` coleta de cada `ValidatableObject<T>` instância, especificando valores para a regra de validação `ValidationMessage` propriedade, que especifica a mensagem de erro de validação que será exibida se Falha de validação.

## <a name="triggering-validation"></a>Validação de gatilho

A abordagem de validação usada no aplicativo móvel eShopOnContainers manualmente pode disparar a validação de uma propriedade e automaticamente a validação de gatilho quando uma propriedade é alterada.

### <a name="triggering-validation-manually"></a>Disparar a validação manualmente

A validação pode ser disparada manualmente para uma propriedade de modelo de exibição. Por exemplo, isso ocorre no aplicativo móvel eShopOnContainers quando o usuário toca o **Login** botão o `LoginView`, ao usar os serviços de simulação. As chamadas de delegado do comando de `MockSignInAsync` método no `LoginViewModel`, que invoca a validação executando o `Validate` método, que é mostrado no exemplo de código a seguir:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

O `Validate` método executa a validação de nome de usuário e senha inseridos pelo usuário `LoginView`, invocando o método Validate em cada `ValidatableObject<T>` instância. O exemplo de código a seguir mostra o método Validate do `ValidatableObject<T>` classe:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Esse método limpa o `Errors` coleção e, em seguida, recupera nenhuma validação de regras que foram adicionados para o objeto `Validations` coleção. O `Check` método para cada regra de validação recuperado é executado e o `ValidationMessage` valor de propriedade para qualquer regra de validação que falha ao validar os dados é adicionado para o `Errors` coleção do `ValidatableObject<T>` instância. Por fim, o `IsValid` estiver definida, e seu valor é retornado para o método de chamada, que indica se a validação foi bem-sucedida ou falhou.

### <a name="triggering-validation-when-properties-change"></a>Disparar validação quando a alteração de propriedades

A validação também pode ser acionada sempre que uma propriedade associada é alterado. Por exemplo, quando uma associação bidirecional no `LoginView` define o `UserName` ou `Password` propriedade, a validação é disparada. O exemplo de código a seguir demonstra como isso ocorre:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

O [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle associa ao `UserName.Value` propriedade do `ValidatableObject<T>` instância e o controle `Behaviors` coleção tem um `EventToCommandBehavior` instância adicionada a ele. Esse comportamento é executado o `ValidateUserNameCommand` em resposta ao [`TextChanged`] eventos acionados no `Entry`, que é gerado quando o texto no `Entry` alterações. Por sua vez, o `ValidateUserNameCommand` delegado executa o `ValidateUserName` método, que executa o `Validate` método o `ValidatableObject<T>` instância. Portanto, sempre que o usuário insere um caractere no `Entry` controle para o nome de usuário, validação de dados inseridos é executada.

Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Exibindo erros de validação

O aplicativo móvel eShopOnContainers notifica o usuário sobre quaisquer erros de validação, realçando o controle que contém os dados inválidos com uma linha vermelha e exibindo uma mensagem de erro que informa ao usuário por que os dados são inválidos abaixo do controle que contém o dados inválidos. Quando os dados inválidos for corrigidos, a linha muda para preto e a mensagem de erro é removida. Figura 6-2 mostra o LoginView no aplicativo móvel eShopOnContainers quando houver erros de validação.

![](validation-images/validation-login.png "Exibindo erros de validação durante o logon")

**Figura 6-2:** exibindo erros de validação durante o logon

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Realce um controle que contém dados inválidos

O `LineColorBehavior` comportamento anexado é usado para realçar [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controles onde ocorreram erros de validação. O seguinte exemplo de código mostra como o `LineColorBehavior` anexado comportamento está anexado a um `Entry` controle:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

O [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle consome um estilo explícito, que é mostrado no exemplo de código a seguir:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Esse estilo define o `ApplyLineColor` e `LineColor` propriedades de anexado a `LineColorBehavior` anexado comportamento no [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle. Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

Quando o valor da `ApplyLineColor` propriedade anexada é conjunto, ou as alterações de `LineColorBehavior` comportamento anexado executa o `OnApplyLineColorChanged` método, que é mostrado no exemplo de código a seguir:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Os parâmetros para este método fornecem a instância do controle que o comportamento está anexado a e os valores novos e antigos do `ApplyLineColor` propriedade anexada. O `EntryLineColorEffect` classe é adicionada ao controle de [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleta se o `ApplyLineColor` é de propriedade anexada `true`, caso contrário, ele é removido do controle de `Effects` coleção. Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

O `EntryLineColorEffect` subclasses de [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe e é mostrado no exemplo de código a seguir:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

O [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe representa um efeito independente de plataforma que encapsula um efeito interno que é específica da plataforma. Isso simplifica o processo de remoção do efeito, porque não há nenhum acesso de tempo de compilação para as informações de tipo para um efeito específico da plataforma. O `EntryLineColorEffect` chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva que é especificada em cada classe de efeito específico da plataforma.

O seguinte exemplo de código mostra o `eShopOnContainers.EntryLineColorEffect` implementação para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

O `OnAttached` método recupera o controle nativo para o xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar e atualiza a cor da linha chamando o `UpdateLineColor` método. O `OnElementPropertyChanged` substituição responde às alterações de propriedade ligável no `Entry` controle atualizando a cor da linha, se o anexo `LineColor` alterações de propriedade, ou o [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propriedade o `Entry`as alterações. Para obter mais informações sobre os efeitos, consulte [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando os dados válidos são inseridos no [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle, ele aplicará uma linha preta até a parte inferior do controle, para indicar que não há nenhum erro de validação. Figura 6-3 mostra um exemplo disso.

![](validation-images/validation-blackline.png "Linha preta indicando que nenhum erro de validação")

**Figura 6-3**: linha preta indicando que nenhum erro de validação

O [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle também tem um [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) adicionado ao seu [ `Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) coleção. O seguinte exemplo de código mostra o `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Isso [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) monitora o `UserName.IsValid` propriedade e se ele é o valor fica `false`, ele executa o [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/), as alterações que o `LineColor` anexado propriedade do `LineColorBehavior` anexado comportamento para vermelho. Figura 6-4 mostra um exemplo disso.

![](validation-images/validation-redline.png "Linha vermelha indicando um erro de validação")

**Figura 6-4**: linha vermelha indicando um erro de validação

A linha de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle permanecerá vermelho enquanto os dados inseridos são inválidos, caso contrário, ela será alterada para preto para indicar que os dados inseridos são válidos.

Para obter mais informações sobre gatilhos, consulte [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Exibindo mensagens de erro

A interface do usuário exibe mensagens de erro de validação em controles de rótulo abaixo de cada controle cujos dados Falha na validação. O seguinte exemplo de código mostra o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) que exibe uma mensagem de erro de validação se o usuário não tiver inserido um nome de usuário válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) associa ao `Errors` propriedade do objeto de modelo de exibição que está sendo validado. O `Errors` propriedade é fornecida pelo `ValidatableObject<T>` classe e é do tipo `List<string>`. Porque o `Errors` propriedade pode conter vários erros de validação, o `FirstValidationErrorConverter` instância é usada para recuperar o primeiro erro da coleção para exibição.

## <a name="summary"></a>Resumo

O aplicativo móvel eShopOnContainers executa a validação de cliente síncrona de propriedades do modelo de exibição e notifica o usuário de quaisquer erros de validação, realçando o controle que contém os dados inválidos e exibindo mensagens de erro que informam o usuário Por que os dados são inválidos.

Exibir as propriedades de modelo que exigem a validação são do tipo `ValidatableObject<T>`e cada `ValidatableObject<T>` instância tem regras de validação adicionadas ao seu `Validations` propriedade. Validação é chamada a partir do modelo de exibição chamando o `Validate` método o `ValidatableObject<T>` instância, que recupera a validação de regras e executa-los contra o `ValidatableObject<T>` `Value` propriedade. Erros de validação são colocados no `Errors` propriedade do `ValidatableObject<T>`instância e o `IsValid` propriedade do `ValidatableObject<T>` instância é atualizada para indicar se a validação foi bem-sucedida ou falha.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
