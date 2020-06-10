---
title: Descrição de " Xamarin.Forms controles de hierarquia de classe": "os desenvolvedores devem estar familiarizados com a hierarquia de tipos usados para criar a interface do usuário de um Xamarin.Forms aplicativo".
MS. Prod: xamarin MS. AssetID: C89E6B98-464D-4BBE-BF11-13A5FCBBF420 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 01/07/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.FormsHierarquia de classes de controles

Xamarin.Formsé composto de centenas de tipos, em vários namespaces. Os desenvolvedores devem estar mais familiarizados com a hierarquia dos tipos usados para criar a interface do usuário de um Xamarin.Forms aplicativo, que reside no `Xamarin.Forms` namespace.

Esses tipos podem ser divididos em páginas, layouts, exibições e células. Uma Xamarin.Forms página geralmente ocupa a tela inteira e todos os tipos de página derivam da [`Page`](xref:Xamarin.Forms.Page) classe. As páginas geralmente contêm um layout e todos os tipos de layout derivam da [`Layout`](xref:Xamarin.Forms.Layout) classe. Um layout geralmente contém exibições e, possivelmente, outros layouts, e todos os tipos de exibição derivam, por fim, da [`View`](xref:Xamarin.Forms.View) classe. Finalmente, as células são controles especializados que são usados em dados de exibição [`TableView`](xref:Xamarin.Forms.TableView) nos [`ListView`](xref:Xamarin.Forms.ListView) controles e. Páginas, layouts, exibições e células são todas derivadas da [`Element`](xref:Xamarin.Forms.Element) classe.

O diagrama de classe a seguir mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin.Forms :

[![Xamarin.FormsDiagrama de classes de controles](class-hierarchy-images/class-diagram.png "[! Parar. Diagrama de classe de controles NO-LOC (Xamarin. Forms)]")](class-hierarchy-images/class-diagram-large.png#lightbox "[! Parar. Diagrama de classe de controles NO-LOC (Xamarin. Forms)]")

> [!NOTE]
> Uma versão de alta resolução do diagrama de classe pode ser baixada [aqui](class-hierarchy-images/class-diagram-high-resolution.png). No entanto, observe que o diagrama mostra apenas um único tipo de Shell.

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsReferência de controles](~/xamarin-forms/user-interface/controls/index.md)
