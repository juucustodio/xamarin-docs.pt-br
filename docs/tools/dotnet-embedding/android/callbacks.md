---
title: Retornos de chamada no Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 2d1d5b8985d132e5a5839e3cd23aaec32fc3815a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292526"
---
# <a name="callbacks-on-android"></a>Retornos de chamada no Android

Chamar o Java de C# é, de certa forma, um *negócio arriscado*. Isso significa que há um *padrão* para retornos de chamada de C# para Java; no entanto, é mais complicado do que gostaríamos.

Abordaremos as três opções para fazer retornos de chamada que fazem mais sentido para Java:

- Classes abstratas
- Interfaces
- Métodos virtuais

## <a name="abstract-classes"></a>Classes abstratas

Essa é a rota mais fácil para retornos de chamada, portanto, recomendo o uso de `abstract` se você estiver apenas tentando obter um retorno de chamada trabalhando na forma mais simples.

Vamos começar com uma classe C# que desejamos que o Java implemente:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Aqui estão os detalhes para fazer isso funcionar:

- `[Register]` gera um bom nome de pacote em Java--você receberá um nome de pacote gerado automaticamente sem ele.
- A subclasse `Java.Lang.Object` sinaliza para a inserção .NET para executar a classe por meio do gerador Java do Xamarin. Android.
- Construtor vazio: é o que você vai querer usar do código Java.
- Construtor de `(IntPtr, JniHandleOwnership)`: é o que o Xamarin. Android usará C#para criar o-equivalente de objetos Java.
- `[Export]` sinaliza o Xamarin. Android para expor o método a Java. Também podemos alterar o nome do método, já que o mundo Java gosta de usar os métodos em minúsculas.

Em seguida, vamos criar C# um método para testar o cenário:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```

`JavaCallbacks` poderia ser qualquer classe para testar isso, desde que seja uma `Java.Lang.Object`.

Agora, execute a inserção do .NET em seu assembly .NET para gerar um AAR. Consulte o [Guia de introdução](~/tools/dotnet-embedding/get-started/java/android.md) para obter detalhes.

Depois de importar o arquivo AAR para Android Studio, vamos escrever um teste de unidade:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```

Então, nós:

- Implementado o `AbstractClass` em Java com um tipo anônimo
- Certificar-se de que nossa instância retorna `"Java"` do Java
- Verificado se nossa instância retorna `"Java"` deC#
- Adição de `throws Throwable`, C# como os construtores estão marcados com `throws`

Se executarmos esse teste de unidade como está, ele falharia com um erro, como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

O que falta aqui é um tipo de `Invoker`. Essa é uma subclasse de `AbstractClass` que encaminha C# chamadas para Java. Se um objeto Java entrar no C# mundo e o tipo C# equivalente for abstrato, o Xamarin. Android procurará automaticamente um C# tipo com o sufixo `Invoker` para uso no C# código.

O Xamarin. Android usa esse `Invoker` padrão para projetos de associação Java entre outras coisas.

Aqui está nossa implementação de `AbstractClassInvoker`:

```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Há muito pouco acontecendo aqui, nós:

- Adicionada uma classe com o sufixo `Invoker` que as subclasses `AbstractClass`
- Adição de `class_ref` para manter a referência JNI à classe Java que subclasses nossa C# classe
- Adição de `id_gettext` para manter a referência de JNI para o método de `getText` Java
- Um construtor de `(IntPtr, JniHandleOwnership)` incluído
- Implementado `ThresholdType` e `ThresholdClass` como um requisito para o Xamarin. Android saber detalhes sobre o `Invoker`
- `GetText` necessário para pesquisar o método Java `getText` com a assinatura JNI apropriada e chamá-lo
- `Dispose` é necessário apenas para limpar a referência a `class_ref`

Depois de adicionar essa classe e gerar um novo AAR, nosso teste de unidade passa. Como você pode ver, esse padrão para retornos de chamada não é o *ideal*, mas factível.

Para obter detalhes sobre a interoperabilidade Java, consulte a documentação incrível do [Xamarin. Android](~/android/platform/java-integration/working-with-jni.md) sobre este assunto.

## <a name="interfaces"></a>Interfaces

As interfaces são muito semelhantes às classes abstratas, exceto por um detalhe: o Xamarin. Android não gera Java para elas. Isso ocorre porque, antes da inserção do .NET, não há muitos cenários em que o Java C# implementaria uma interface.

Digamos que tenhamos a seguinte C# interface:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` sinais para a incorporação de .NET que esta é uma interface Xamarin. Android, mas, caso contrário, isso é exatamente o mesmo que uma classe `abstract`.

Como o Xamarin. Android não gerará o código Java para essa interface no momento, adicione o Java a C# seguir ao seu projeto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Você pode colocar o arquivo em qualquer lugar, mas certifique-se de definir sua ação de Build como `AndroidJavaSource`. Isso sinalizará a inserção do .NET para copiá-lo para o diretório apropriado para ser compilado em seu arquivo AAR.

Em seguida, a implementação de `Invoker` será exatamente a mesma:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Depois de gerar um arquivo AAR, em Android Studio poderíamos escrever o seguinte teste de unidade de passagem:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Métodos virtuais

A substituição de um `virtual` em Java é possível, mas não uma ótima experiência.

Vamos supor que você tenha a seguinte C# classe:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Se você seguiu o exemplo de classe `abstract` acima, ele funcionará com exceção de um detalhe: _o Xamarin. Android não pesquisará o `Invoker`_ .

Para corrigir isso, modifique a C# classe a ser `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Isso não é o ideal, mas tem esse cenário funcionando. O Xamarin. Android pegará o `VirtualClassInvoker` e o Java poderá usar `@Override` no método.

## <a name="callbacks-in-the-future"></a>Retornos de chamada no futuro

Há algumas coisas que poderíamos fazer para melhorar esses cenários:

1. `throws Throwable` em C# construtores é corrigido nesta [PR](https://github.com/xamarin/java.interop/pull/170).
1. Faça o gerador de Java nas interfaces de suporte do Xamarin. Android.
    - Isso remove a necessidade de adicionar um arquivo de origem Java com uma ação de compilação de `AndroidJavaSource`.
1. Faça uma maneira para o Xamarin. Android carregar um `Invoker` para classes virtuais.
    - Isso remove a necessidade de marcar a classe em nosso `virtual` exemplo `abstract`.
1. Gerar classes de `Invoker` para a inserção do .NET automaticamente
    - Isso será complicado, mas factível. O Xamarin. Android já está fazendo algo semelhante a este para projetos de associação Java.

Há muito trabalho a ser feito aqui, mas esses aprimoramentos na inserção do .NET são possíveis.

## <a name="further-reading"></a>Leitura adicional

- [Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Pesquisa do Android preliminar](~/tools/dotnet-embedding/android/index.md)
- [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
- [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)
