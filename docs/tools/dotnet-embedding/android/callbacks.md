---
title: Retornos de chamada no Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: bfe12d84510707ff6e81aae2b5b20be7e9cacd59
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293062"
---
# <a name="callbacks-on-android"></a>Retornos de chamada no Android

Chamar o Java de C# é, de certa forma, um *negócio arriscado*. Isso significa que há um *padrão* para retornos de chamada de C# para Java; no entanto, é mais complicado do que gostaríamos.

Abordaremos as três opções para fazer retornos de chamada que fazem mais sentido para Java:

- Classes abstratas
- Interfaces
- Métodos virtuais

## <a name="abstract-classes"></a>Classes abstratas

Essa é a rota mais fácil para retornos de chamada, portanto, recomendo `abstract` usar se você estiver apenas tentando obter um retorno de chamada trabalhando na forma mais simples.

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

- `[Register]`gera um bom nome de pacote em Java – você receberá um nome de pacote gerado automaticamente sem ele.
- A subclasse `Java.Lang.Object` sinaliza para a inserção .net para executar a classe por meio do gerador Java do Xamarin. Android.
- Construtor vazio: é o que você vai querer usar do código Java.
- `(IntPtr, JniHandleOwnership)`Construtor: é o que o Xamarin. Android usará para C#criar o-equivalente de objetos Java.
- `[Export]`sinaliza o Xamarin. Android para expor o método a Java. Também podemos alterar o nome do método, já que o mundo Java gosta de usar os métodos em minúsculas.

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

`JavaCallbacks`poderia ser qualquer classe para testar isso, desde que seja um `Java.Lang.Object`.

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

- Implementou `AbstractClass` o em Java com um tipo anônimo
- Certificar-se de que `"Java"` nossa instância retorna do Java
- Certificar-se de que `"Java"` nossa instância retorna deC#
- Adicionado `throws Throwable`, pois C# os construtores estão marcados com`throws`

Se executarmos esse teste de unidade como está, ele falharia com um erro, como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

O que está faltando aqui é `Invoker` um tipo. Essa é uma subclasse de `AbstractClass` que encaminha C# chamadas para Java. Se um objeto Java entrar no C# mundo e o tipo C# equivalente for abstrato, o Xamarin. Android procurará automaticamente um C# tipo com o sufixo `Invoker` para uso no C# código.

O Xamarin. Android usa `Invoker` esse padrão para projetos de associação Java entre outras coisas.

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

- Adicionada uma classe com o sufixo `Invoker` que as subclasses`AbstractClass`
- Adicionado `class_ref` para manter a referência de JNI para a classe Java que subclasse nossa C# classe
- Adicionado `id_gettext` para manter a referência de JNI para o `getText` método Java
- Incluído um `(IntPtr, JniHandleOwnership)` Construtor
- Implementado `ThresholdType` e`ThresholdClass` como um requisito para o Xamarin. Android saber detalhes sobre o`Invoker`
- `GetText`necessário para pesquisar o método `getText` Java com a assinatura JNI apropriada e chamá-lo
- `Dispose`é necessário apenas para limpar a referência a`class_ref`

Depois de adicionar essa classe e gerar um novo AAR, nosso teste de unidade passa. Como você pode ver, esse padrão para retornos de chamada não é o *ideal*, mas factível.

Para obter detalhes sobre a interoperabilidade Java, consulte a documentação incrível do [Xamarin. Android](~/android/platform/java-integration/working-with-jni.md) sobre este assunto.

## <a name="interfaces"></a>Interfaces

As interfaces são muito semelhantes às classes abstratas, exceto por um detalhe: O Xamarin. Android não gera Java para eles. Isso ocorre porque, antes da inserção do .NET, não há muitos cenários em que o Java C# implementaria uma interface.

Digamos que tenhamos a seguinte C# interface:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject`sinaliza para o .net incorporando que esta é uma interface Xamarin. Android, mas, caso contrário, isso é exatamente `abstract` o mesmo que uma classe.

Como o Xamarin. Android não gerará o código Java para essa interface no momento, adicione o Java a C# seguir ao seu projeto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Você pode colocar o arquivo em qualquer lugar, mas certifique-se de definir sua `AndroidJavaSource`ação de compilação como. Isso sinalizará a inserção do .NET para copiá-lo para o diretório apropriado para ser compilado em seu arquivo AAR.

Em seguida, `Invoker` a implementação será exatamente a mesma:

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

A substituição `virtual` de um em Java é possível, mas não uma ótima experiência.

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

Se você seguiu o `abstract` exemplo de classe acima, ele funcionaria com exceção de um detalhe: _O Xamarin. Android não pesquisará o `Invoker`_ .

Para corrigir isso, modifique a C# classe a ser `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Isso não é o ideal, mas tem esse cenário funcionando. O Xamarin. Android pegará o `VirtualClassInvoker` e o Java pode `@Override` usar no método.

## <a name="callbacks-in-the-future"></a>Retornos de chamada no futuro

Há algumas coisas que poderíamos fazer para melhorar esses cenários:

1. `throws Throwable`os C# construtores on são corrigidos nesta [PR](https://github.com/xamarin/java.interop/pull/170).
1. Faça o gerador de Java nas interfaces de suporte do Xamarin. Android.
    - Isso remove a necessidade de adicionar um arquivo de origem Java com uma ação `AndroidJavaSource`de compilação de.
1. Faça uma maneira para o Xamarin. Android carregar um `Invoker` para classes virtuais.
    - Isso remove a necessidade de marcar a classe em nosso `virtual` exemplo `abstract`.
1. Gerar `Invoker` classes para inserção de .NET automaticamente
    - Isso será complicado, mas factível. O Xamarin. Android já está fazendo algo semelhante a este para projetos de associação Java.

Há muito trabalho a ser feito aqui, mas esses aprimoramentos na inserção do .NET são possíveis.

## <a name="further-reading"></a>Leitura adicional

- [Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Pesquisa do Android preliminar](~/tools/dotnet-embedding/android/index.md)
- [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)
