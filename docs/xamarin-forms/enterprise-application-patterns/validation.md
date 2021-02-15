---
title: Validação em aplicativos empresariais
description: Este capítulo explica como o aplicativo móvel eShopOnContainers executa a validação da entrada do usuário. Isso inclui a especificação de regras de validação, o disparo da validação e a exibição de erros de validação.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a5b7ecb0272543785c98bfff12bec7fcccf7fc8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373933"
---
# <a name="validation-in-enterprise-apps"></a>Validação em aplicativos empresariais

> [!NOTE]
> Este livro eletrônico foi publicado na Primavera de 2017 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas parte do material está desatualizada.

Qualquer aplicativo que aceite entrada de usuários deve garantir que a entrada seja válida. Um aplicativo poderia, por exemplo, verificar se há entradas que contenham apenas caracteres em um intervalo específico, tem um determinado comprimento ou corresponde a um formato específico. Sem a validação, um usuário pode fornecer dados que causam falha no aplicativo. A validação impõe regras de negócio e impede que um invasor insira dados mal-intencionados.

No contexto do padrão Model-View-ViewModel (MVVM), um modelo de exibição ou modelo geralmente será necessário para executar a validação de dados e sinalizar quaisquer erros de validação para o modo de exibição para que o usuário possa corrigi-los. O aplicativo móvel eShopOnContainers executa a validação síncrona do lado do cliente das propriedades do modelo de exibição e notifica o usuário sobre quaisquer erros de validação, destacando o controle que contém os dados inválidos e exibindo mensagens de erro que informam ao usuário por que os dados são inválidos. A Figura 6-1 mostra as classes envolvidas na execução da validação no aplicativo móvel eShopOnContainers.

[![Classes de validação no aplicativo móvel eShopOnContainers](validation-images/validation.png)](validation-images/validation-large.png#lightbox "Classes de validação no aplicativo móvel eShopOnContainers")

**Figura 6-1** : classes de validação no aplicativo móvel eShopOnContainers

As propriedades do modelo de exibição que exigem validação são do tipo `ValidatableObject<T>` e cada `ValidatableObject<T>` instância tem regras de validação adicionadas à sua `Validations` propriedade. A validação é invocada do modelo de exibição chamando o `Validate` método da `ValidatableObject<T>` instância, que recupera as regras de validação e as executa em relação à `ValidatableObject<T>` `Value` propriedade. Todos os erros de validação são colocados na `Errors` propriedade da `ValidatableObject<T>` instância do e a `IsValid` propriedade da `ValidatableObject<T>` instância é atualizada para indicar se a validação foi bem-sucedida ou falhou.

A notificação de alteração de propriedade é fornecida pela `ExtendedBindableObject` classe e, portanto, um [`Entry`](xref:Xamarin.Forms.Entry) controle pode `IsValid` ser associado à propriedade de `ValidatableObject<T>` instância na classe do modelo de exibição para ser notificado de se os dados inseridos são ou não válidos.

## <a name="specifying-validation-rules"></a>Especificando regras de validação

As regras de validação são especificadas pela criação de uma classe derivada da `IValidationRule<T>` interface, que é mostrada no exemplo de código a seguir:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Essa interface especifica que uma classe de regra de validação deve fornecer um `boolean` `Check` método que é usado para executar a validação necessária e uma `ValidationMessage` propriedade cujo valor é a mensagem de erro de validação que será exibida se a validação falhar.

O exemplo de código a seguir mostra a `IsNotNullOrEmptyRule<T>` regra de validação, que é usada para executar a validação do nome de usuário e senha inseridos pelo usuário no `LoginView` ao usar serviços fictícios no aplicativo móvel eShopOnContainers:

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

O `Check` método retorna um `boolean` que indica se o argumento de valor é `null` , vazio ou se consiste apenas em caracteres de espaço em branco.

Embora não seja usado pelo aplicativo móvel eShopOnContainers, o exemplo de código a seguir mostra uma regra de validação para validar endereços de email:

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

O `Check` método retorna um `boolean` que indica se o argumento de valor é um endereço de email válido ou não. Isso é obtido pesquisando o argumento de valor da primeira ocorrência do padrão de expressão regular especificado no `Regex` Construtor. Se o padrão de expressão regular foi encontrado na cadeia de caracteres de entrada pode ser determinado verificando o valor da `Match` Propriedade do objeto `Success` .

> [!NOTE]
> Às vezes, a validação da propriedade pode envolver propriedades dependentes. Um exemplo de propriedades dependentes é quando o conjunto de valores válidos para a propriedade A depende do valor específico que foi definido na propriedade B. Para verificar se o valor da propriedade A é um dos valores permitidos envolveria a recuperação do valor da propriedade B. Além disso, quando o valor da propriedade B for alterado, a propriedade A deverá ser revalidada.

## <a name="adding-validation-rules-to-a-property"></a>Adicionando regras de validação a uma propriedade

No aplicativo móvel eShopOnContainers, exiba as propriedades do modelo que exigem validação são declaradas como sendo do tipo `ValidatableObject<T>` , em que `T` é o tipo dos dados a serem validados. O exemplo de código a seguir mostra um exemplo de duas propriedades desse tipo:

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

Para que a validação ocorra, as regras de validação devem ser adicionadas à `Validations` coleção de cada `ValidatableObject<T>` instância, conforme demonstrado no exemplo de código a seguir:

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

Esse método adiciona a `IsNotNullOrEmptyRule<T>` regra de validação à `Validations` coleção de cada `ValidatableObject<T>` instância, especificando valores para a propriedade da regra de validação `ValidationMessage` , que especifica a mensagem de erro de validação que será exibida se a validação falhar.

## <a name="triggering-validation"></a>Gatilho de validação

A abordagem de validação usada no aplicativo móvel eShopOnContainers pode disparar manualmente a validação de uma propriedade e acionar automaticamente a validação quando uma propriedade é alterada.

### <a name="triggering-validation-manually"></a>Acionando a validação manualmente

A validação pode ser acionada manualmente para uma propriedade de modelo de exibição. Por exemplo, isso ocorre no aplicativo móvel eShopOnContainers quando o usuário toca no botão de **logon** no `LoginView` , ao usar serviços fictícios. O delegado de comando chama o `MockSignInAsync` método no `LoginViewModel` , que invoca a validação executando o `Validate` método, que é mostrado no exemplo de código a seguir:

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

O `Validate` método executa a validação do nome de usuário e a senha inseridos pelo usuário no `LoginView` , invocando o método Validate em `ValidatableObject<T>` cada instância. O exemplo de código a seguir mostra o método Validate da `ValidatableObject<T>` classe:

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

Esse método limpa a `Errors` coleção e, em seguida, recupera as regras de validação que foram adicionadas à `Validations` coleção do objeto. O `Check` método para cada regra de validação recuperada é executado e o `ValidationMessage` valor da propriedade para qualquer regra de validação que não valida os dados é adicionado à `Errors` coleção da `ValidatableObject<T>` instância. Por fim, a `IsValid` propriedade é definida e seu valor é retornado para o método de chamada, indicando se a validação foi bem-sucedida ou falhou.

### <a name="triggering-validation-when-properties-change"></a>Acionando a validação quando as propriedades são alteradas

A validação também pode ser disparada sempre que uma propriedade associada for alterada. Por exemplo, quando uma associação bidirecional em `LoginView` define a `UserName` propriedade ou, a `Password` validação é disparada. O exemplo de código a seguir demonstra como isso ocorre:

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

O [`Entry`](xref:Xamarin.Forms.Entry) controle é associado à `UserName.Value` propriedade da `ValidatableObject<T>` instância e a coleção do controle `Behaviors` tem uma `EventToCommandBehavior` instância adicionada a ele. Esse comportamento executa o `ValidateUserNameCommand` em resposta ao `TextChanged` acionamento do evento [] no `Entry` , que é gerado quando o texto é `Entry` alterado. Por sua vez, o `ValidateUserNameCommand` delegado executa o `ValidateUserName` método, que executa o `Validate` método na `ValidatableObject<T>` instância. Portanto, toda vez que o usuário insere um caractere no `Entry` controle para o nome de usuário, a validação dos dados inseridos é executada.

Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing-behaviors).

## <a name="displaying-validation-errors"></a>Exibindo erros de validação

O aplicativo móvel eShopOnContainers notifica o usuário sobre quaisquer erros de validação, destacando o controle que contém os dados inválidos com uma linha vermelha e exibindo uma mensagem de erro que informa ao usuário por que os dados são inválidos abaixo do controle que contém os dados inválidos. Quando os dados inválidos são corrigidos, a linha é alterada para preto e a mensagem de erro é removida. A Figura 6-2 mostra o LoginView no aplicativo móvel eShopOnContainers quando erros de validação estão presentes.

![Exibindo erros de validação durante o logon](validation-images/validation-login.png)

**Figura 6-2:** Exibindo erros de validação durante o logon

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Realçando um controle que contém dados inválidos

O `LineColorBehavior` comportamento anexado é usado para realçar os [`Entry`](xref:Xamarin.Forms.Entry) controles em que ocorreram erros de validação. O exemplo de código a seguir mostra como o `LineColorBehavior` comportamento anexado é anexado a um `Entry` controle:

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

O [`Entry`](xref:Xamarin.Forms.Entry) controle consome um estilo explícito, que é mostrado no exemplo de código a seguir:

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

Esse estilo define as `ApplyLineColor` `LineColor` Propriedades anexadas e do `LineColorBehavior` comportamento anexado no [`Entry`](xref:Xamarin.Forms.Entry) controle. Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

Quando o valor da `ApplyLineColor` Propriedade anexada é definido, ou muda, o `LineColorBehavior` comportamento anexado executa o `OnApplyLineColorChanged` método, que é mostrado no exemplo de código a seguir:

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

Os parâmetros para esse método fornecem a instância do controle ao qual o comportamento está anexado e os valores novos e antigos da `ApplyLineColor` Propriedade anexada. A `EntryLineColorEffect` classe é adicionada à coleção do controle [`Effects`](xref:Xamarin.Forms.Element.Effects) se a `ApplyLineColor` Propriedade anexada for `true` , caso contrário, será removida da coleção do controle `Effects` . Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing-behaviors).

O `EntryLineColorEffect` subclasse é a [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe e é mostrada no exemplo de código a seguir:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

A [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe representa um efeito independente de plataforma que encapsula um efeito interno específico da plataforma. Isso simplifica o processo de remoção do efeito, porque não há nenhum acesso de tempo de compilação às informações de tipo para um efeito específico da plataforma. O `EntryLineColorEffect` chama o construtor da classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e na ID exclusiva especificada em cada classe de efeito específica da plataforma.

O exemplo de código a seguir mostra a `eShopOnContainers.EntryLineColorEffect` implementação para IOS:

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

O `OnAttached` método recupera o controle nativo para o Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) controle e atualiza a cor da linha chamando o `UpdateLineColor` método. A `OnElementPropertyChanged` substituição responderá às alterações de propriedade vinculáveis no `Entry` controle atualizando a cor da linha se a propriedade anexada `LineColor` for alterada ou a [`Height`](xref:Xamarin.Forms.VisualElement.Height) propriedade das `Entry` alterações. Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando dados válidos são inseridos no [`Entry`](xref:Xamarin.Forms.Entry) controle, ele aplica uma linha preta na parte inferior do controle, para indicar que não há nenhum erro de validação. A Figura 6-3 mostra um exemplo disso.

![Linha preta que indica nenhum erro de validação](validation-images/validation-blackline.png)

**Figura 6-3** : linha preta que indica nenhum erro de validação

O [`Entry`](xref:Xamarin.Forms.Entry) controle também tem um [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) adicionado à sua [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) coleção. O exemplo de código a seguir mostra `DataTrigger` :

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

Isso [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) monitora a `UserName.IsValid` propriedade e, se for o valor `false` , ele executará o [`Setter`](xref:Xamarin.Forms.Setter) , que altera a `LineColor` Propriedade anexada do `LineColorBehavior` comportamento anexado para vermelho. A Figura 6-4 mostra um exemplo disso.

![Linha vermelha indicando erro de validação](validation-images/validation-redline.png)

**Figura 6-4** : linha vermelha indicando erro de validação

A linha no [`Entry`](xref:Xamarin.Forms.Entry) controle permanecerá vermelha enquanto os dados inseridos forem inválidos, caso contrário, ele será alterado para preto para indicar que os dados inseridos são válidos.

Para obter mais informações sobre gatilhos, consulte [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Exibindo mensagens de erro

A interface do usuário exibe mensagens de erro de validação em controles de rótulo abaixo de cada controle cujos dados falharam na validação. O exemplo de código a seguir mostra o [`Label`](xref:Xamarin.Forms.Label) que exibe uma mensagem de erro de validação se o usuário não tiver inserido um nome de usuário válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada uma [`Label`](xref:Xamarin.Forms.Label) é associada à `Errors` Propriedade do objeto de modelo de exibição que está sendo validado. A `Errors` propriedade é fornecida pela `ValidatableObject<T>` classe e é do tipo `List<string>` . Como a `Errors` propriedade pode conter vários erros de validação, a `FirstValidationErrorConverter` instância é usada para recuperar o primeiro erro da coleção para exibição.

## <a name="summary"></a>Resumo

O aplicativo móvel eShopOnContainers executa a validação síncrona do lado do cliente das propriedades do modelo de exibição e notifica o usuário sobre quaisquer erros de validação, destacando o controle que contém os dados inválidos e exibindo mensagens de erro que informam ao usuário por que os dados são inválidos.

As propriedades do modelo de exibição que exigem validação são do tipo `ValidatableObject<T>` e cada `ValidatableObject<T>` instância tem regras de validação adicionadas à sua `Validations` propriedade. A validação é invocada do modelo de exibição chamando o `Validate` método da `ValidatableObject<T>` instância, que recupera as regras de validação e as executa em relação à `ValidatableObject<T>` `Value` propriedade. Todos os erros de validação são colocados na `Errors` propriedade da `ValidatableObject<T>` instância do e a `IsValid` propriedade da `ValidatableObject<T>` instância é atualizada para indicar se a validação foi bem-sucedida ou falhou.

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
