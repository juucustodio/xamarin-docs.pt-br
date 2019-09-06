---
title: Atualizações do SiriKit no iOS 11
description: Este documento descreve como trabalhar com o SiriKit no iOS 11. Em particular, ele examina como trabalhar com tarefas e observações e como fornecer nomes alternativos para um aplicativo.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/07/2017
ms.openlocfilehash: d4fab992121ad6a2b272012f7249df5ed8427513
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286736"
---
# <a name="sirikit-updates-in-ios-11"></a>Atualizações do SiriKit no iOS 11

O SiriKit foi introduzido no iOS 10, com um número de domínios de serviço (incluindo exercícios, reserva de Ride e fazer chamadas). Consulte a [seção SiriKit](~/ios/platform/sirikit/index.md) para obter os conceitos de SiriKit e como implementar SiriKit em seu aplicativo.

![Demonstração da lista de tarefas do Siri](sirikit-images/sirikit.png)

SiriKit no iOS 11 adiciona esses domínios de intenção novos e atualizados:

- [**Listas e observações**](#listsnotes) – novo! Fornece uma API para que os aplicativos processem tarefas e observações.
- **Códigos visuais** – novo! Siri pode exibir códigos QR para compartilhar informações de contato ou participar de transações de pagamento.
- **Pagamentos** – adição de tentativas de pesquisa e transferência para interações de pagamento.
- **Reserva de Ride** – adição de cancelamentos de retenções e comentários.

Outros novos recursos incluem:

- [**Nomes de aplicativo alternativos**](#alternativenames) – fornece aliases que ajudam os clientes a instruir o Siri a direcionar seu aplicativo oferecendo nomes/pronúncias alternativos.
- **Iniciando exercícios** – fornece a capacidade de iniciar um treinamento em segundo plano.

Alguns desses recursos são explicados abaixo. Para obter mais detalhes sobre os outros, consulte a [documentação do SiriKit da Apple](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listas e observações

O novo domínio listas e observações fornece uma API para que os aplicativos processem tarefas e observações por meio de solicitações de voz Siri.

**Tarefas**

- Ter um título e um status de conclusão.
- Opcionalmente, inclua um prazo e um local.

**Observações**

- Ter um título e um campo de conteúdo.

As tarefas e as anotações podem ser organizadas em grupos. O restante desta seção descreve como implementar esse novo domínio com SiriKit, usando o exemplo de [SiriKit TasksNotes](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample).

### <a name="how-to-process-a-sirikit-request"></a>Como processar uma solicitação de SiriKit

Processe uma solicitação SiriKit seguindo estas etapas:

1. **Resolver** – validar parâmetros e solicitar mais informações do usuário (se necessário).
2. **Confirmar** – validação final e verificação de que a solicitação pode ser processada.
3. **Identificador** – execute a operação (Atualizando dados ou executando operações de rede).

As duas primeiras etapas são opcionais (embora incentivadas) e a etapa final é necessária.
Há instruções mais detalhadas na [seção SiriKit](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Resolver e confirmar métodos

Esses métodos opcionais permitem que seu código execute a validação, selecione padrões ou solicite informações adicionais do usuário.

Como exemplo, para a `IINCreateTaskListIntent` interface, o método necessário é. `HandleCreateTaskList` Há quatro métodos opcionais que fornecem mais controle sobre a interação Siri:

- `ResolveTitle`– Valida o título, define um título padrão (se apropriado) ou sinaliza que os dados não são necessários.
- `ResolveTaskTitles`– Valida a lista de tarefas faladas pelo usuário.
- `ResolveGroupName`– Valida o nome do grupo, escolhe um grupo padrão ou sinaliza que os dados não são necessários.
- `ConfirmCreateTaskList`– Valida que seu código pode executar a operação solicitada, mas não a executa (somente os `Handle*` métodos devem modificar dados).

### <a name="handle-the-intent"></a>Tratar a intenção

Há seis tentativas no domínio de listas e observações, três para tarefas e três para anotações.
Os métodos que você deve implementar para lidar com essas intenções são:

- Para tarefas:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Para observações:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Cada método tem um tipo de tentativa específico passado para ele, que contém todas as informações Siri analisadas da solicitação do usuário (e possivelmente atualizadas nos `Resolve*` métodos e `Confirm*` ).
Seu aplicativo deve analisar os dados fornecidos, executar algumas ações para armazenar ou processar os dados e retornar um resultado que Siri fala e mostra ao usuário.

### <a name="response-codes"></a>Códigos de resposta

Os métodos `Handle*` obrigatórios `Confirm*` e opcionais indicam um código de resposta definindo um valor no objeto que eles passam para seu manipulador de conclusão. As `INCreateTaskListIntentResponseCode` respostas são provenientes da enumeração:

- `Ready`– Retorna durante a fase de confirmação (isto é, `Confirm*` de um método, mas não `Handle*` de um método).
- `InProgress`– Usado para tarefas de execução longa (como uma operação de rede/servidor).
- `Success`– Responde com os detalhes da operação bem-sucedida (somente de um `Handle*` método).
- `Failure`– Significa que ocorreu um erro e a operação não pôde ser concluída.
- `RequiringAppLaunch`– Não pode ser processado pela intenção, mas a operação é possível no aplicativo.
- `Unspecified`– Não use: a mensagem de erro será exibida para o usuário.

Saiba mais sobre esses métodos e respostas na [documentação das listas e notas do SiriKit](https://developer.apple.com/documentation/sirikit/lists_and_notes)da Apple.

### <a name="implementing-lists-and-notes"></a>Implementando listas e observações

O [exemplo de SiriKit TasksNotes](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample) foi criado usando as etapas a seguir para adicionar suporte a SiriKit a um aplicativo Ios em branco.

Primeiro, para adicionar suporte a SiriKit, siga estas etapas para seu aplicativo iOS:

1. **SiriKit** de tique em **direitos. plist**.
2. Adicione a chave de **Descrição de uso de privacidade – Siri** ao **info. plist**, juntamente com uma mensagem para seus clientes.
3. Chame o `INPreferences.RequestSiriAuthorization` método no aplicativo para solicitar que o usuário permita interações de siri.
4. Adicione SiriKit à sua ID do aplicativo no portal do desenvolvedor e recrie seus perfis de provisionamento para incluir o novo direito.

Em seguida, adicione um novo projeto de extensão ao seu aplicativo para manipular solicitações Siri:

1. Clique com o botão direito do mouse em sua solução e escolha **adicionar > adicionar novo projeto...** .
2. Escolha a **extensão de > do iOS >** modelo de extensão de intenções.
3. Dois novos projetos serão adicionados: Intenção e IntentUI. A personalização da interface do usuário é opcional, portanto, o exemplo inclui apenas o código no projeto de **intenção** .

O projeto de extensão é onde todas as solicitações SiriKit serão processadas. Como uma extensão separada, ela não tem automaticamente nenhuma maneira de se comunicar com seu aplicativo principal – isso normalmente é resolvido pela implementação do armazenamento de arquivos compartilhado usando grupos de aplicativos.

#### <a name="configure-the-intenthandler"></a>Configurar o IntentHandler

A `IntentHandler` classe é o ponto de entrada para solicitações Siri – cada tentativa é passada para o `GetHandler` método, que retorna um objeto que pode manipular a solicitação.

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

A classe deve herdar `INExtension`de e, como o exemplo vai manusear as listas e as tentativas de observações, ela também `IINNotebookDomainHandling`implementa.

> [!NOTE]
> - Há uma convenção no .net para que as interfaces sejam prefixadas com um `I`capital, que o Xamarin segue ao ligar protocolos do SDK do Ios.
> - O Xamarin também preserva os nomes de tipo do iOS e a Apple usa os dois primeiros caracteres em nomes de tipo para refletir a estrutura à qual um tipo pertence.
> - Para a `Intents` estrutura, os tipos são prefixados com `IN*` (por exemplo, `INExtension`), mas _não_ são interfaces.
> - Ele também segue os C# `I` `IINAddTasksIntentHandling`protocolos (que se tornam interfaces no) que acabam com dois s, como.

#### <a name="handling-intents"></a>Manipulando tentativas

Cada tentativa (adicionar tarefa, definir atributo de tarefa, etc) é implementada em um único método semelhante ao mostrado abaixo. O método deve executar três funções principais:

1. **Processar a intenção** – os dados analisados pelo Siri são disponibilizados em um `intent` objeto específico para o tipo de intenção. Seu aplicativo pode ter validado esses dados usando `Resolve*` métodos opcionais.
2. **Validar e atualizar o armazenamento de dados** – salve os dados no sistema de arquivos (usando grupos de aplicativos para que o aplicativo IOS principal também possa acessá-lo) ou por meio de uma solicitação de rede.
3. **Fornecer resposta** – use o `completion` manipulador para enviar uma resposta de volta ao Siri para leitura/exibição para o usuário:

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

Observe que `null` é passado como o segundo parâmetro para a resposta – esse é o parâmetro de atividade do usuário e, quando não é fornecido, um valor padrão será usado.
Você pode definir um tipo de atividade personalizada, desde que seu aplicativo IOS ofereça suporte a `NSUserActivityTypes` ele por meio da chave no **info. plist**. Em seguida, você pode manipular esse caso quando seu aplicativo é aberto e executar operações específicas (como abrir para um controlador de exibição relevante e carregar os dados da operação Siri).

O exemplo também codifica o `Success` resultado, mas em cenários reais, o relatório de erros adequado deve ser adicionado.

### <a name="test-phrases"></a>Frases de teste

As seguintes frases de teste devem funcionar no aplicativo de exemplo:

- "Faça uma lista de compras com maçãs, bananas e pêras no TasksNotes"
- "Adicionar WWDC de tarefas no TasksNotes"
- "Adicionar WWDC de tarefas à lista de treinamento no TasksNotes"
- "Mark WWDC como concluído em TasksNotes"
- "No TasksNotes, lembre-me de comprar um iPhone quando eu chegar à casa"
- "Mark comprar iPhone como concluído em TasksNotes"
- "Lembre-me de sair de casa às 8h em TasksNotes"

![Criar um novo exemplo de lista](sirikit-images/createtasklist-sml.png) ![Definir tarefa como exemplo completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> O simulador do iOS 11 dá suporte ao teste com Siri (diferentemente das versões anteriores).
>
> Se estiver testando em dispositivos reais, não se esqueça de configurar a ID do aplicativo e os perfis de provisionamento para o suporte do SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomes alternativos

Esse novo recurso do iOS 11 significa que você pode configurar nomes alternativos para seu aplicativo para ajudar os usuários a dispará-lo corretamente com o Siri. Adicione as seguintes chaves ao arquivo **info. plist** do projeto do aplicativo IOS:

![Info. plist mostrando chaves e valores de nome de aplicativo alternativos](sirikit-images/alternative-names.png)

Com os nomes de aplicativo alternativos definidos, as seguintes frases também funcionarão para o aplicativo de exemplo (que, na verdade, é chamado de **TasksNotes**):

- "Faça uma lista de compras com maçãs, bananas e pêras no _MonkeyNotes_"
- "Adicionar WWDC de tarefas no _MonkeyTodo_"


## <a name="troubleshooting"></a>Solução de problemas

Alguns erros que você pode encontrar ao executar o exemplo ou adicionar SiriKit a seus próprios aplicativos:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Exceção de Objective-C lançada.  Nome: Motivo do NSInternalInconsistencyException: Uso da classe < Inferences: o 0x60400082ff00 > de um aplicativo requer o direito com. Apple. Developer. Siri. Você habilitou o recurso Siri em seu projeto Xcode?_

- SiriKit é marcado em **direitos. plist**.
- **Direitos. o plist** é configurado nas opções de **projeto > Build > assinatura de pacote do IOS**.

  [![Opções de projeto mostrando direitos definidos corretamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (para implantação de dispositivo) A ID do aplicativo tem SiriKit habilitado e perfil de provisionamento baixado.


## <a name="related-links"></a>Links relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Exemplo de SiriKit de TasksNotes](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)
- [O que há de novo no SiriKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)
