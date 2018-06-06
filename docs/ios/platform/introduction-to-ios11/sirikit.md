---
title: Atualizações de SiriKit no iOS 11
description: Este documento descreve como trabalhar com SiriKit no iOS 11. Em particular, ele examina como trabalhar com tarefas e observações e como fornecer nomes alternativos para um aplicativo.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/07/2017
ms.openlocfilehash: 28160b40c97b8cc62fae95d3643801f1c4cc5e93
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787579"
---
# <a name="sirikit-updates-in-ios-11"></a>Atualizações de SiriKit no iOS 11

SiriKit foi introduzido no iOS 10, com um número de domínios de serviço (inclusive exercícios, jornada de reserva e fazer chamadas). Consulte o [SiriKit seção](~/ios/platform/sirikit/index.md) para SiriKit conceitos e como implementar SiriKit em seu aplicativo.

![Demonstração de lista de tarefas do Siri](sirikit-images/sirikit.png)

SiriKit no iOS 11 adiciona esses domínios intenção novos e atualizados:

- [**Lista e notas** ](#listsnotes) – novo! Fornece uma API para aplicativos para processar tarefas e anotações.
- **Códigos Visual** – novo! Siri pode exibir códigos QR para compartilhar informações de contato ou participar de transações de pagamento.
- **Pagamentos** – adicionado propósitos de pesquisa e a transferência para interações de pagamento.
- **Propagar reserva** – adicionado Cancelar tentativas jornada e comentários.

Outros novos recursos incluem:

- [**Nomes de aplicativo alternativo** ](#alternativenames) – fornece aliases que ajudam os clientes informam Siri para seu aplicativo de destino por meio de nomes/pronúncia alternativa.
- **Iniciando exercícios** – fornece a capacidade de iniciar um treinamento em segundo plano.

Alguns desses recursos são explicados abaixo. Para obter mais detalhes sobre os outros, consulte [documentação de SiriKit da Apple](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listas e notas

O novo domínio de listas e notas fornece uma API para aplicativos para processar tarefas e anotações por meio de solicitações de voz Siri.

**Tarefas**

- Ter um título e um status de conclusão.
- Inclua opcionalmente um prazo e um local.

**Observações**

- Ter um título e um campo de conteúdo.

Tarefas e anotações podem ser organizadas em grupos. O restante desta seção descreve como implementar esse novo domínio com SiriKit, usando o [TasksNotes SiriKit exemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Como processar uma solicitação de SiriKit

Processe uma solicitação de SiriKit seguindo estas etapas:

1. **Resolver** – validar parâmetros e solicitar mais informações do usuário (se necessário).
2. **Confirmar** – validação Final e verificação de que a solicitação pode ser processada.
3. **Tratar** – executar a operação (atualização de dados ou executar operações de rede).

As duas primeiras etapas são opcionais (embora incentivados), e a etapa final é necessária.
Há instruções mais detalhadas a [SiriKit seção](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Resolver e confirme os métodos

Esses métodos opcionais permitem que seu código executar validação, selecionados padrões ou solicitar informações adicionais do usuário.

Por exemplo, para o `IINCreateTaskListIntent` interface, o método necessário é `HandleCreateTaskList`. Há quatro métodos opcionais que fornecem mais controle sobre a interação de Siri:

- `ResolveTitle` – Valida o título, define um título padrão (se apropriado) ou sinais de que os dados não são necessários.
- `ResolveTaskTitles` – Valida a lista de tarefas falado pelo usuário.
- `ResolveGroupName` – Valida o nome do grupo, escolhe um grupo padrão ou sinais de que os dados não são necessários.
- `ConfirmCreateTaskList` – Valida que seu código pode executar a operação solicitada, mas não executa (somente o `Handle*` métodos devem modificar dados).

### <a name="handle-the-intent"></a>Lidar com a intenção

Há seis tentativas no domínio listas e anotações, três para tarefas e três para anotações.
Os métodos que você deve implementar para lidar com essas tentativas são:

- Para tarefas:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Para obter notas:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Cada método tem um tentativa tipo específico passado para ele, que contém todas as informações Siri foi analisado da solicitação do usuário (e possivelmente atualizado no `Resolve*` e `Confirm*` métodos).
Seu aplicativo deve analisar os dados fornecidos, em seguida, executar algumas ações para armazenar ou processo caso contrário, os dados e retornar um resultado que Siri fala e mostra ao usuário.

### <a name="response-codes"></a>Códigos de resposta

Obrigatório `Handle*` e opcionais `Confirm*` métodos indicam um código de resposta, definindo um valor no objeto que eles passam para o processador de conclusão. As respostas são provenientes de `INCreateTaskListIntentResponseCode` enumeração:

- `Ready` – Retorna durante a fase de confirmação (ie. de um `Confirm*` método, mas não de um `Handle*` método).
- `InProgress` – Usado para tarefas de longa execução (por exemplo, uma operação de rede/servidor).
- `Success` – Responde com os detalhes da operação bem-sucedida (somente de um `Handle*` método).
- `Failure` – Significa que ocorreu um erro e não foi possível concluir a operação.
- `RequiringAppLaunch` – Não pode ser processada por tentativa, mas a operação é possível no aplicativo.
- `Unspecified` – Não usar: mensagem de erro será exibida ao usuário.

Saiba mais sobre esses métodos e as respostas em da Apple [SiriKit lista e notas de documentação](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementação de listas e notas

O [TasksNotes SiriKit exemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) foi criado usando as etapas a seguir para adicionar suporte de SiriKit para um aplicativo iOS em branco.

Primeiro, para adicionar suporte SiriKit, siga estas etapas para seu aplicativo iOS:

1. Escala **SiriKit** na **Entitlements.plist**.
2. Adicionar o **privacidade – descrição de uso do Siri** chave para **Info. plist**, juntamente com uma mensagem para seus clientes.
3. Chamar o `INPreferences.RequestSiriAuthorization` método no aplicativo, para solicitar ao usuário para permitir que o Siri interações.
4. Adicionar SiriKit a sua ID do aplicativo no Portal do desenvolvedor e recriar seu perfis de provisionamento para incluir o direito de novo.

Em seguida, adicione um novo projeto de extensão para seu aplicativo para tratar as solicitações de Siri:

1. Clique com botão direito em sua solução e escolha **Adicionar > Adicionar novo projeto...** .
2. Escolha o **iOS > extensão > tentativas extensão** modelo.
3. Dois novos projetos serão adicionados: tentativa e IntentUI. Personalizando a interface do usuário é opcional, portanto o exemplo inclui apenas o código de **intenção** projeto.

O projeto de extensão é onde todas as solicitações de SiriKit serão processadas. Como uma extensão separada, ele não tem automaticamente uma maneira de se comunicar com seu aplicativo principal – isso geralmente é resolvido com a implementação de armazenamento de arquivos compartilhados usando grupos de aplicativos.

#### <a name="configure-the-intenthandler"></a>Configurar o IntentHandler

O `IntentHandler` classe é o ponto de entrada para solicitações de Siri – cada tentativa é passada para o `GetHandler` método, que retorna um objeto que pode lidar com a solicitação.

O código a seguir mostra uma implementação simples:

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

A classe deve herdar de `INExtension`, e como o exemplo vai manipular listas e notas de tentativas, ele também implementa `IINNotebookDomainHandling`.

> [!NOTE]
> - Há uma convenção no .NET para interfaces para ser prefixados com uma letra maiuscula `I`, que cumpre o Xamarin quando a associação de protocolos do SDK do iOS.
> - Xamarin também preserva os nomes de tipo do iOS e Apple usa os dois primeiros caracteres em nomes de tipo para refletir a estrutura que um tipo pertence.
> - Para o `Intents` framework, os tipos são prefixados com `IN*` (por exemplo. `INExtension`), mas são _não_ interfaces.
> - Ele também segue protocolos (que se tornam interfaces em c#) terminar com dois `I`s, como `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>Tratamento de tentativas

Cada tentativa (Adicionar tarefa, defina o atributo de tarefa, etc.) é implementada em um único método semelhante ao mostrado a seguir. O método deve executar três funções principais:

1. **Processar a intenção** – os dados analisados por Siri são disponibilizados em um `intent` específicos para o tipo da intenção do objeto. Seu aplicativo pode validar dados usando opcional `Resolve*` métodos.
2. **Validar e atualizar o repositório de dados** – salvar dados no sistema de arquivos (usar grupos de aplicativos para que o aplicativo do iOS principal também pode acessá-lo) ou por meio de uma solicitação de rede.
3. **Fornecer a resposta** – Use o `completion` manipulador para enviar uma resposta ao Siri/exibição de leitura para o usuário:

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

Observe que `null` é passado como o segundo parâmetro para a resposta – este é o parâmetro de atividade de usuário, e quando não for fornecido, um valor padrão será usado.
Você pode definir um tipo de atividade personalizada, contanto que seu aplicativo iOS dá suporte a ele por meio de `NSUserActivityTypes` chave em **Info. plist**. Você pode lidar com esse caso, quando seu aplicativo é aberto e executar operações específicas (como abrindo a um controlador de exibição relevantes e carregar os dados da operação de Siri).

O exemplo também codifica o `Success` resultados, mas em situações reais, relatório de erros apropriado deve ser adicionado.

### <a name="test-phrases"></a>Expressões de teste

As seguintes frases de teste devem funcionar no aplicativo de exemplo:

- "Tornar uma lista de compras com maçãs e bananas Peras em TasksNotes"
- "Adicionar tarefa WWDC em TasksNotes"
- "Adicionar tarefa WWDC à lista de treinamento em TasksNotes"
- "Marca participar WWDC como concluído em TasksNotes"
- "Em TasksNotes lembrar-me para comprar um iphone quando recebo home"
- "Marca comprar iPhone como concluído em TasksNotes"
- "Lembrar-me deixar início às 8 em TasksNotes"

![Criar um novo exemplo de lista](sirikit-images/createtasklist-sml.png) ![Tarefa definida como exemplo completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> O iOS 11 simulador dá suporte a testes com Siri (ao contrário das versões anteriores).
>
> Se o teste em dispositivos reais, não se esqueça de configurar sua ID do aplicativo e suportam a perfis de provisionamento para SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomes alternativos

Esse novo recurso do iOS 11 significa que você pode configurar os nomes alternativos para seu aplicativo ajudar os usuários dispará-lo corretamente com o Siri. Adicione as seguintes chaves para o **Info. plist** arquivo do projeto de aplicativo do iOS:

![Info. plist mostrando valores e chaves de nome alternativo do aplicativo](sirikit-images/alternative-names.png)

Com o conjunto de nomes alternativos de aplicativo, as seguintes frases também funcionará para o aplicativo de exemplo (que é realmente chamado **TasksNotes**):

- "Tornar uma lista de compras com maçãs e bananas Peras em _MonkeyNotes_"
- "Adicionar tarefas WWDC em _MonkeyTodo_"


## <a name="troubleshooting"></a>Solução de problemas

Alguns erros que você pode encontrar durante a execução do exemplo ou adicionando SiriKit aos seus aplicativos:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Objective-C exceção é gerada.  Nome: NSInternalInconsistencyException motivo: uso da classe < INPreferences: 0x60400082ff00 > de um aplicativo requer o direito com.apple.developer.siri. Você habilitou o recurso Siri no seu projeto Xcode?_

- SiriKit está marcada em **Entitlements.plist**.
- **Entitlements.plist** é configurado no **opções de projeto > compilar > iOS de assinatura do pacote**.

  [![Opções de projeto mostrando que direitos definida corretamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (para implantação de dispositivo) ID do aplicativo tem SiriKit habilitado e perfil de provisionamento baixado.


## <a name="related-links"></a>Links relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Exemplo de SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [Novidades no SiriKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)
