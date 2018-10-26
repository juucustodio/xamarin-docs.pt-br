---
title: Atualizações de SiriKit no iOS 11
description: Este documento descreve como trabalhar com SiriKit no iOS 11. Em particular, ele examina como trabalhar com tarefas e anotações e como fornecer nomes alternativos para um aplicativo.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/07/2017
ms.openlocfilehash: 7e895dc2865880ec2789a40f8cdf047a20f8693b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111032"
---
# <a name="sirikit-updates-in-ios-11"></a>Atualizações de SiriKit no iOS 11

SiriKit foi introduzido no iOS 10, com um número de domínios do serviço (incluindo exercícios, jornada de reserva e como fazer chamadas). Consulte a [SiriKit seção](~/ios/platform/sirikit/index.md) para conceitos de SiriKit e como implementar SiriKit em seu aplicativo.

![Demonstração de lista de tarefas do Siri](sirikit-images/sirikit.png)

SiriKit no iOS 11 adiciona esses domínios intenção novos e atualizados:

- [**Lista e notas** ](#listsnotes) – novo! Fornece uma API para aplicativos para processar tarefas e anotações.
- **Códigos de Visual** – novo! Siri pode exibir os códigos QR para compartilhar informações de contato ou participar de transações de pagamento.
- **Pagamentos** – adicionado as intenções de pesquisa e transferência para interações de pagamento.
- **Surfar reserva** – adicionado Cancelar tentativas de corrida e comentários.

Outros novos recursos incluem:

- [**Nomes de aplicativo alternativos** ](#alternativenames) – Siri para direcionar seu aplicativo, oferecendo nomes alternativa/pronúncia de informar fornece aliases que ajudam os clientes.
- **Iniciando exercícios físicos** – fornece a capacidade de iniciar um treinamento em segundo plano.

Alguns desses recursos são explicados abaixo. Para obter mais detalhes sobre os outros, consulte [documentação de SiriKit da Apple](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listas e notas

O novo domínio de listas e notas fornece uma API para aplicativos para processar tarefas e anotações por meio de solicitações de voz Siri.

**Tarefas**

- Ter um título e um status de conclusão.
- Opcionalmente, inclua um prazo final e um local.

**Observações**

- Ter um título e um campo de conteúdo.

Tarefas e anotações podem ser organizadas em grupos. O restante desta seção descreve como implementar esse novo domínio com SiriKit, usando o [TasksNotes SiriKit exemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Como processar uma solicitação de SiriKit

Processe uma solicitação de SiriKit seguindo estas etapas:

1. **Resolver** – validar parâmetros e solicitar mais informações do usuário (se necessário).
2. **Confirmar** – validação Final e verificação de que a solicitação pode ser processada.
3. **Lidar com** – executar a operação (atualização de dados ou executar operações de rede).

As duas primeiras etapas são opcionais (embora estimulado), e a etapa final é necessária.
Há instruções mais detalhadas na [SiriKit seção](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Resolver e confirmar os métodos

Esses métodos opcionais permitem que seu código executar a validação, selecionados padrões ou informações adicionais de solicitação do usuário.

Por exemplo, para o `IINCreateTaskListIntent` interface, o método necessário é `HandleCreateTaskList`. Há quatro métodos opcionais que fornecem mais controle sobre a interação do Siri:

- `ResolveTitle` – Valida o título, define um título padrão (se apropriado) ou sinaliza que os dados não são necessários.
- `ResolveTaskTitles` – Valida a lista de tarefas falado pelo usuário.
- `ResolveGroupName` – Valida o nome do grupo, escolhe um grupo padrão ou sinaliza que os dados não são necessários.
- `ConfirmCreateTaskList` – Valida que o seu código pode executar a operação solicitada, mas não executar (somente o `Handle*` métodos devem modificar dados).

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

Cada método tem um propósito tipo específico passado para ele, que contém todas as informações que Siri tenha analisado da solicitação do usuário (e possivelmente atualizado na `Resolve*` e `Confirm*` métodos).
Seu aplicativo deve analisar os dados fornecidos, em seguida, executar algumas ações para armazenar ou processar caso contrário, os dados e retornar um resultado que Siri fala e mostre ao usuário.

### <a name="response-codes"></a>Códigos de resposta

Exigida `Handle*` opcionais e `Confirm*` métodos indicam um código de resposta, definindo um valor no objeto que passam para o seu manipulador de conclusão. As respostas são provenientes de `INCreateTaskListIntentResponseCode` enumeração:

- `Ready` – Retorna durante a fase de confirmação (ie. de um `Confirm*` método, mas não de um `Handle*` método).
- `InProgress` – Usado para tarefas de longa execução (como uma operação de rede/servidor).
- `Success` – Responde com os detalhes da operação bem-sucedida (somente de um `Handle*` método).
- `Failure` – Significa que ocorreu um erro e a operação não pôde ser concluída.
- `RequiringAppLaunch` – Não podem ser processadas pela intenção, mas a operação é possível no aplicativo.
- `Unspecified` – Não usar: mensagem de erro será exibida ao usuário.

Saiba mais sobre esses métodos e as respostas em da Apple [SiriKit lista e documentação de notas](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementação de listas e notas

O [TasksNotes SiriKit exemplo](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) foi criado usando as seguintes etapas para adicionar suporte de SiriKit a um aplicativo iOS em branco.

Primeiro, para adicionar suporte de SiriKit, siga estas etapas para seu aplicativo iOS:

1. Escala **SiriKit** na **Entitlements. plist**.
2. Adicione a **privacidade – descrição de uso do Siri** chave **Info. plist**, juntamente com uma mensagem para seus clientes.
3. Chamar o `INPreferences.RequestSiriAuthorization` método no aplicativo, solicitar ao usuário para permitir que Siri interações.
4. Adicionar SiriKit à sua ID de aplicativo no Portal do desenvolvedor e crie novamente os perfis de provisionamento para incluir o novo direito.

Em seguida, adicione um novo projeto de extensão ao seu aplicativo para lidar com solicitações do Siri:

1. Clique duas vezes em sua solução e escolha **Adicionar > Adicionar novo projeto...** .
2. Escolha o **iOS > extensão > extensão de tentativas** modelo.
3. Dois novos projetos serão adicionados: intenção e IntentUI. Personalizando a interface do usuário é opcional, portanto, o exemplo inclui apenas o código na **intenção** projeto.

O projeto de extensão é onde todas as solicitações de SiriKit serão processadas. Como uma extensão separada, ele não tem automaticamente qualquer forma de se comunicar com seu aplicativo principal – isso geralmente é resolvido com a implementação de armazenamento de arquivos compartilhados usando grupos de aplicativos.

#### <a name="configure-the-intenthandler"></a>Configurar o IntentHandler

O `IntentHandler` classe é o ponto de entrada para solicitações do Siri – cada tentativa é passada para o `GetHandler` método, que retorna um objeto que pode manipular a solicitação.

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

A classe deve herdar de `INExtension`, e como o exemplo vai à manipulação de listas e notas intenções, também implementa `IINNotebookDomainHandling`.

> [!NOTE]
> - Há uma convenção no .NET para interfaces para ser prefixado com uma letra maiuscula `I`, que segue o Xamarin ao associar os protocolos do SDK do iOS.
> - Xamarin também preserva os nomes de tipo do iOS e Apple usa os dois primeiros caracteres em nomes de tipo para refletir a estrutura de um tipo ao qual pertence.
> - Para o `Intents` framework, os tipos são prefixados com `IN*` (por exemplo. `INExtension`), mas esses são _não_ interfaces.
> - Também segue esse protocolos (que se tornam interfaces no C#) acabar com dois `I`s, como `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>: Identificando intenções

Cada tentativa (Adicionar tarefa, defina o atributo de tarefa, etc.) é implementada em um único método, semelhante ao mostrado abaixo. O método deve executar três funções principais:

1. **Processar a intenção** – os dados analisados por Siri são disponibilizados em um `intent` específico para o tipo da intenção do objeto. Seu aplicativo pode validar dados usando opcional `Resolve*` métodos.
2. **Valide e atualize o repositório de dados** , salve dados no sistema de arquivos (usando grupos de aplicativos para que o aplicativo iOS principal também pode acessá-lo), ou por meio de uma solicitação de rede.
3. **Fornecer resposta** – Use o `completion` manipulador para enviar uma resposta de volta ao Siri para leitura/exibição ao usuário:

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

Observe que `null` é passado como o segundo parâmetro para a resposta – esse é o parâmetro de atividade de usuário e quando não for fornecido, um valor padrão será usado.
Você pode definir um tipo de atividade personalizada, desde que seu aplicativo iOS dá suporte a ele por meio de `NSUserActivityTypes` principais no **Info. plist**. Você pode lidar com isso quando seu aplicativo é aberto e executar operações específicas (por exemplo, abrindo a um controlador de exibição relevante e carregar os dados da operação de Siri).

O exemplo também codifica o `Success` resultado, mas em cenários reais, relatório de erros adequado deve ser adicionado.

### <a name="test-phrases"></a>Frases de teste

As seguintes frases de teste devem funcionar no aplicativo de exemplo:

- "Tornar uma lista de compras com maçãs, bananas e Peras em TasksNotes"
- "Adicionar tarefa WWDC no TasksNotes"
- "Adicionar tarefa WWDC à lista de treinamento em TasksNotes"
- "Marca participe WWDC como concluído no TasksNotes"
- "Em TasksNotes lembrar-me a comprar um iphone, quando eu chegar em casa"
- "Marca comprar iPhone concluído no TasksNotes"
- "Lembrar-me deixar início às 8:00 em TasksNotes"

![Criar um novo exemplo de lista](sirikit-images/createtasklist-sml.png) ![Tarefa definida como um exemplo completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> O iOS 11 Simulator dá suporte a testes com Siri (ao contrário de versões anteriores).
>
> Se o teste em dispositivos reais, não se esqueça de configurar sua ID do aplicativo e dar suporte a perfis de provisionamento para SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomes alternativos

Esse novo recurso do iOS 11 significa que você pode configurar nomes alternativos para seu aplicativo ajudar os usuários dispará-lo corretamente com Siri. Adicione as seguintes chaves para o **Info. plist** arquivo de projeto de aplicativo do iOS:

![Info. plist mostrando valores e chaves de nome alternativo do aplicativo](sirikit-images/alternative-names.png)

Com o conjunto de nomes alternativos de aplicativo, as seguintes frases também funcionarão para o aplicativo de exemplo (que, na verdade, é denominado **TasksNotes**):

- "Fazer uma lista de compras com maçãs, bananas e Peras na _MonkeyNotes_"
- "Adicionar tarefa WWDC na _MonkeyTodo_"


## <a name="troubleshooting"></a>Solução de problemas

Alguns erros que podem ocorrer durante a execução do exemplo ou adicionando SiriKit aos seus próprios aplicativos:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Exceção Objective-C é gerada.  Nome: NSInternalInconsistencyException motivo: uso da classe < INPreferences: 0x60400082ff00 > de um aplicativo requer o direito com.apple.developer.siri. Você habilitou o recurso de Siri em seu projeto Xcode?_

- SiriKit está marcada na **Entitlements. plist**.
- **Entitlements. plist** está configurado na **opções do projeto > Build > assinatura do pacote iOS**.

  [![Opções de projeto mostrando que direitos definidas corretamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (para implantação de dispositivo) ID do aplicativo tem SiriKit habilitado e perfil de provisionamento baixado.


## <a name="related-links"></a>Links relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Exemplo de SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [O que há de novo no SiriKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)
