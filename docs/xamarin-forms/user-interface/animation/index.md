---
Título: ' animação em Xamarin.Forms ' Descrição: ' Xamarin.Forms inclui sua própria infraestrutura de animação que é simples para criar animações simples, enquanto também é versátil o suficiente para criar animações complexas. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="animation-in-xamarinforms"></a>Animação emXamarin.Forms

_O Xamarin. Forms inclui sua própria infraestrutura de animação que é simples para criar animações simples, ao mesmo tempo que também é versátil o suficiente para criar animações complexas._

As Xamarin.Forms classes de animação visam propriedades diferentes de elementos visuais, com uma animação típica que altera progressivamente uma propriedade de um valor para outro em um período de tempo. Observe que não há nenhuma interface XAML para as Xamarin.Forms classes de animação. No entanto, as animações podem ser encapsuladas em [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md) e, em seguida, referenciadas do XAML.

## <a name="simple-animations"></a>[Animações simples](simple.md)

A [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornece métodos de extensão que podem ser usados para construir animações simples que giram, dimensionam, traduzem e esmaecem as [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias. Este artigo demonstra como criar e cancelar animações usando a `ViewExtensions` classe.

## <a name="easing-functions"></a>[Funções de easing](easing.md)

Xamarin.Formsinclui uma [`Easing`](xref:Xamarin.Forms.Easing) classe que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução. Este artigo demonstra como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas.

## <a name="custom-animations"></a>[Animações personalizadas](custom.md)

A [`Animation`](xref:Xamarin.Forms.Animation) classe é o bloco de construção de todas as Xamarin.Forms animações, com os métodos de extensão na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe que cria um ou mais `Animation` objetos. Este artigo demonstra como usar a `Animation` classe para criar e cancelar animações, sincronizar várias animações e criar animações personalizadas que animam propriedades que não são animadas pelos métodos de animação existentes.
