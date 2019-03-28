---
title: Validação em aplicativos empresariais
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers realiza a validação da entrada do usuário. Isso inclui especificar regras de validação, acionar a validação e exibindo erros de validação.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 22b5fe703486f0ded3a5b91241e3fe5ce41bbc98
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507091"
---
# <a name="validation-in-enterprise-apps"></a>Validação em aplicativos empresariais

Qualquer aplicativo que aceita entrada de usuários deve garantir que a entrada é válida. Um aplicativo poderia, por exemplo, verificar para a entrada que contenha apenas caracteres em um intervalo específico, é de um determinado tamanho ou corresponde a um determinado formato. Sem validação, um usuário pode fornecer dados que faz com que o aplicativo falhe. Validação impõe regras de negócio e impede que um invasor injetando dados mal-intencionados.

No contexto do Model-View-ViewModel (MVVM) padrão, um modelo de exibição ou modelo geralmente precisará executar a validação de dados e sinalizar erros de validação para o modo de exibição para que o usuário possa corrigi-los. O aplicativo móvel do eShopOnContainers realiza a validação de cliente síncrona de propriedades do modelo de exibição e notifica o usuário de erros de validação, realçando o controle que contém os dados inválidos e exibindo mensagens de erro informar ao usuário Por que os dados são inválidos. Figura 6-1 mostra as classes envolvidas ao executar a validação no aplicativo móvel do eShopOnContainers.

[![](validation-images/validation.png "Classes de validação no aplicativo móvel do eShopOnContainers")](validation-images/validation-large.png#lightbox "classes de validação no aplicativo móvel do eShopOnContainers")

**Figura 6-1**: Classes de validação no aplicativo móvel do eShopOnContainers

Exibir propriedades de modelo que exigem validação são do tipo `ValidatableObject<T>`e cada `ValidatableObject<T>` instância tem regras de validação adicionadas ao seu `Validations` propriedade. Validação é chamada de modelo de exibição chamando o `Validate` método do `ValidatableObject<T>` instância, que recupera a validação de regras e os executa em relação a `ValidatableObject<T>` `Value` propriedade. Erros de validação são colocados na `Errors` propriedade do `ValidatableObject<T>` instância e o `IsValid` propriedade do `ValidatableObject<T>` instância será atualizada para indicar se a validação foi bem-sucedida ou falhou.

Notificação de alteração de propriedade é fornecida pelos `ExtendedBindableObject` classe e, portanto, um [ `Entry` ](xref:Xamarin.Forms.Entry) controle pode vincular o `IsValid` propriedade do `ValidatableObject<T>` instância na classe de modelo de exibição para ser notificado sobre se deve ou não os dados inseridos são válidos.

## <a name="specifying-validation-rules"></a>Especificar regras de validação

Regras de validação são especificadas, criando uma classe que deriva de `IValidationRule<T>` interface, que é mostrado no exemplo de código a seguir:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Essa interface Especifica que uma classe de regra de validação deve fornecer um `boolean` `Check` método que é usado para executar a validação necessária, e um `ValidationMessage` propriedade cujo valor é a mensagem de erro de validação que será exibida se a validação falhará.

O seguinte exemplo de código mostra a `IsNotNullOrEmptyRule<T>` regra de validação, que é usada para executar a validação de nome de usuário e senha inserida pelo usuário na `LoginView` ao usar os serviços de simulação no aplicativo móvel do eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

O `Check` método retorna um `boolean` que indica se o argumento de valor é `null`, vazio ou consiste apenas em caracteres de espaço em branco.

Embora não seja usado pelo aplicativo móvel do eShopOnContainers, o exemplo de código a seguir mostra uma regra de validação para validar endereços de email:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

O `Check` método retorna um `boolean` que indica se o argumento de valor é um endereço de email válido. Isso é feito por meio da pesquisa o argumento de valor para a primeira ocorrência do padrão de expressão regular especificada no `Regex` construtor. Se o padrão de expressão regular foi encontrado na cadeia de entrada pode ser determinado examinando o valor da `Match` do objeto `Success` propriedade.

> [!NOTE]
> Validação de propriedade às vezes, pode envolver a propriedades dependentes. Um exemplo de propriedades dependentes é quando o conjunto de valores válidos para A propriedade depende do valor específico que foi definido na propriedade B. Para verificar se o valor da propriedade de um é um dos valores permitidos envolveria a recuperar o valor da propriedade B. Além disso, quando o valor da propriedade B é alterada, a propriedade um precisariam ser revalidado.

## <a name="adding-validation-rules-to-a-property"></a>Adicionando regras de validação para uma propriedade

No aplicativo móvel do eShopOnContainers, exibir propriedades de modelo que exigem validação são declaradas como sendo do tipo `ValidatableObject<T>`, onde `T` é o tipo de dados a ser validado. O exemplo de código a seguir mostra um exemplo de duas propriedades:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para a validação ocorrer, as regras de validação devem ser adicionadas para o `Validations` coleção de cada `ValidatableObject<T>` da instância, conforme demonstrado no exemplo de código a seguir:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Esse método adiciona o `IsNotNullOrEmptyRule<T>` regra de validação para o `Validations` coleção de cada `ValidatableObject<T>` instância, especificando valores para a regra de validação `ValidationMessage` propriedade, que especifica a mensagem de erro de validação que será exibida se a validação falhará.

## <a name="triggering-validation"></a>Acionar a validação

A abordagem de validação usada no aplicativo móvel do eShopOnContainers pode disparar manualmente a validação de uma propriedade de automaticamente gatilho quando uma propriedade é alterada.

### <a name="triggering-validation-manually"></a>Acionar a validação manualmente

Validação pode ser disparada manualmente para uma propriedade de modelo de exibição. Por exemplo, isso ocorre no aplicativo móvel do eShopOnContainers quando o usuário toca a **Login** botão o `LoginView`, ao usar os serviços de simulação. O comando delegado chama o `MockSignInAsync` método na `LoginViewModel`, que invoca a validação executando o `Validate` método, que é mostrado no exemplo de código a seguir:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

O `Validate` método realiza a validação do nome de usuário e senha inserida pelo usuário na `LoginView`, invocando o método Validate em cada `ValidatableObject<T>` instância. O exemplo de código a seguir mostra o método Validate do `ValidatableObject<T>` classe:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Esse método limpa os `Errors` coleta e, em seguida, recupera qualquer validação de regras que foram adicionados para o objeto `Validations` coleção. O `Check` método para cada regra de validação recuperado é executado e o `ValidationMessage` valor da propriedade para qualquer regra de validação que não é possível validar os dados é adicionado para o `Errors` coleção da `ValidatableObject<T>` instância. Por fim, o `IsValid` estiver definida, e seu valor é retornado para o método de chamada, que indica se a validação foi bem-sucedida ou falhou.

### <a name="triggering-validation-when-properties-change"></a>Acionar a validação quando propriedades alteradas

Validação também pode ser acionada sempre que uma propriedade associada é alterado. Por exemplo, quando uma associação bidirecional na `LoginView` define o `UserName` ou `Password` propriedade, a validação é disparada. O exemplo de código a seguir demonstra como isso ocorre:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

O [ `Entry` ](xref:Xamarin.Forms.Entry) controle está associado a `UserName.Value` propriedade do `ValidatableObject<T>` instância e o controle `Behaviors` coleção tem um `EventToCommandBehavior` instância adicionada a ele. Esse comportamento é executado o `ValidateUserNameCommand` em resposta ao [`TextChanged`] disparo de eventos na `Entry`, que é gerado quando o texto no `Entry` alterações. Por sua vez, o `ValidateUserNameCommand` delegado executa o `ValidateUserName` método, que executa o `Validate` método no `ValidatableObject<T>` instância. Portanto, sempre que o usuário insere um caractere no `Entry` controle para o nome de usuário, validação de dados inseridas é executada.

Para obter mais informações sobre comportamentos, consulte [implementação de comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Exibindo erros de validação

O aplicativo móvel do eShopOnContainers notifica o usuário de erros de validação, realçando o controle que contém os dados inválidos com uma linha vermelha e exibindo uma mensagem de erro que informa ao usuário por que os dados são inválidos abaixo o controle que contém o dados inválidos. Quando os dados inválidos for corrigidos, a linha é alterado para preto e a mensagem de erro é removida. Figura 6-2 mostra o LoginView no aplicativo móvel do eShopOnContainers quando houver erros de validação.

![](validation-images/validation-login.png "Exibindo erros de validação durante o logon")

**Figura 6-2:** Exibindo erros de validação durante o logon

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Realce de um controle que contém dados inválidos

O `LineColorBehavior` comportamento anexado é usado para realçar [ `Entry` ](xref:Xamarin.Forms.Entry) controles onde ocorreram erros de validação. O seguinte exemplo de código mostra como o `LineColorBehavior` anexado comportamento está anexado a um `Entry` controle:

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

O [ `Entry` ](xref:Xamarin.Forms.Entry) controle consome um estilo explícito, o que é mostrado no exemplo de código a seguir:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Esse estilo define o `ApplyLineColor` e `LineColor` propriedades de anexado o `LineColorBehavior` anexados a comportamento o [ `Entry` ](xref:Xamarin.Forms.Entry) controle. Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

Quando o valor da `ApplyLineColor` propriedade anexada é o conjunto ou alterações, o `LineColorBehavior` comportamento anexado executa o `OnApplyLineColorChanged` método, que é mostrado no exemplo de código a seguir:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Os parâmetros para esse método fornecem a instância do controle que o comportamento é anexado ao e os valores novos e antigos do `ApplyLineColor` propriedade anexada. O `EntryLineColorEffect` classe é adicionada para o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção se o `ApplyLineColor` propriedade anexada é `true`, caso contrário, ele é removido do controle de `Effects` coleção. Para obter mais informações sobre comportamentos, consulte [implementação de comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

O `EntryLineColorEffect` subclasses a [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) de classe e é mostrado no exemplo de código a seguir:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

O [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe representa um efeito de independente de plataforma que encapsula um efeito interno que é específico da plataforma. Isso simplifica o processo de remoção do efeito, porque não há nenhum acesso de tempo de compilação às informações de tipo para um efeito específico da plataforma. O `EntryLineColorEffect` chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva que é especificada em cada classe de efeito específico da plataforma.

O seguinte exemplo de código mostra o `eShopOnContainers.EntryLineColorEffect` implementação para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

O `OnAttached` método recupera o controle nativo para o xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controlar e atualiza a cor da linha chamando o `UpdateLineColor` método. O `OnElementPropertyChanged` substituição responde às alterações de propriedade associável na `Entry` controle atualizando a cor da linha, se o anexo `LineColor` alterações de propriedade, ou o [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propriedade do `Entry`as alterações. Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando dados válido são inseridos na [ `Entry` ](xref:Xamarin.Forms.Entry) controle, ela será aplicada a uma linha preta na parte inferior do controle, para indicar que não há nenhum erro de validação. Figura 6-3 mostra um exemplo disso.

![](validation-images/validation-blackline.png "Linha preta indicando que nenhum erro de validação")

**Figura 6-3**: Linha preta indicando que nenhum erro de validação

O [ `Entry` ](xref:Xamarin.Forms.Entry) controle também tem uma [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) adicionado ao seu [ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers) coleção. O seguinte exemplo de código mostra o `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Isso [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) monitora o `UserName.IsValid` propriedade e se ele for um valor se torna `false`, ele executa o [ `Setter` ](xref:Xamarin.Forms.Setter), as alterações que o `LineColor` anexado propriedade do `LineColorBehavior` anexado o comportamento para vermelho. Figura 6-4 mostra um exemplo disso.

![](validation-images/validation-redline.png "Linha vermelha indicando o erro de validação")

**Figura 6-4**: Linha vermelha indicando o erro de validação

A linha de [ `Entry` ](xref:Xamarin.Forms.Entry) controle permanecerá vermelho, enquanto os dados inseridos são inválidos, caso contrário, ela será alterada para preto para indicar que os dados inseridos são válidos.

Para obter mais informações sobre gatilhos, consulte [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Exibindo mensagens de erro

A interface do usuário exibe mensagens de erro de validação em controles de rótulo abaixo de cada controle cujos dados de falha na validação. O seguinte exemplo de código mostra a [ `Label` ](xref:Xamarin.Forms.Label) que exibe uma mensagem de erro de validação se o usuário não tiver inserido um nome de usuário válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [ `Label` ](xref:Xamarin.Forms.Label) associa ao `Errors` propriedade do objeto de modelo de exibição que está sendo validado. O `Errors` propriedade é fornecida pelo `ValidatableObject<T>` classe e é do tipo `List<string>`. Porque o `Errors` propriedade pode conter vários erros de validação, o `FirstValidationErrorConverter` instância é usada para recuperar o primeiro erro da coleção para exibição.

## <a name="summary"></a>Resumo

O aplicativo móvel do eShopOnContainers realiza a validação de cliente síncrona de propriedades do modelo de exibição e notifica o usuário de erros de validação, realçando o controle que contém os dados inválidos e exibindo mensagens de erro informar ao usuário Por que os dados são inválidos.

Exibir propriedades de modelo que exigem validação são do tipo `ValidatableObject<T>`e cada `ValidatableObject<T>` instância tem regras de validação adicionadas ao seu `Validations` propriedade. Validação é chamada de modelo de exibição chamando o `Validate` método do `ValidatableObject<T>` instância, que recupera a validação de regras e os executa em relação a `ValidatableObject<T>` `Value` propriedade. Erros de validação são colocados na `Errors` propriedade do `ValidatableObject<T>`instância e o `IsValid` propriedade do `ValidatableObject<T>` instância será atualizada para indicar se a validação foi bem-sucedida ou falhou.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
