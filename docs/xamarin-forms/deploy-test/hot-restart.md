---
Título: "Xamarin Hot restart" Descrição: "este documento descreve como configurar e usar o Xamarin Hot restart para depurar um aplicativo iOS".
MS. Prod: xamarin MS. AssetID: 6BC62A88-9368-41BB-8494-760F2A4805DB MS. Technology: xamarin-Forms autor: maddyleger1 MS. Author: maleger MS. Date: 03/16/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarin-hot-restart-preview"></a>Xamarin Hot Restart (versão prévia)

![Versão prévia do recurso](~/media/shared/preview.png)

O Xamarin Hot Restart permite que você teste rapidamente as alterações em seu aplicativo durante o desenvolvimento, incluindo edições, recursos e referências de código de vários arquivos. Ele envia por push as novas alterações para o pacote de aplicativo existente no alvo da depuração, o que resulta em um ciclo de build e implantação muito mais rápido.

> [!IMPORTANT]
> O Xamarin Hot Restart está disponível atualmente no Visual Studio 2019 versão 16,5 estável e dá suporte a aplicativos iOS usando Xamarin.Forms . O suporte para Visual Studio para Mac e não Xamarin.Forms aplicativos está no roteiro.

## <a name="requirements"></a>Requisitos

- Visual Studio 2019 versão 16,5
- iTunes (64 bits)
- Conta de desenvolvedor da Apple e registro de [programa de desenvolvedor da Apple](https://developer.apple.com/programs) pago


## <a name="initial-setup"></a>Instalação inicial

> [!NOTE]
> O Xamarin Hot Restart está desabilitado por padrão enquanto está em visualização. Você pode habilitá-lo em **ferramentas > opções > ambiente > recursos de visualização > habilitar o Xamarin Hot Restart**.

1. Verifique se o projeto do iOS está definido como o projeto de inicialização e se a configuração da compilação está definida como **depurar | iPhone**.

   1. Se este for um projeto existente, vá para **Build > Configuration Manager…** e garanta que **Implantar** esteja habilitado para o projeto do iOS.

2. Selecione e clique em **Dispositivo Local** na barra de ferramentas para iniciar o assistente de instalação:

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Se o iTunes não estiver instalado, clique em **Baixar iTunes** para baixar o instalador. Clique em **Avançar** quando a instalação do iTunes for concluída.

4. Conecte um dispositivo iOS a seu computador. Se um dispositivo já estava conectado, desconecte-o e, em seguida, reconecte-o. O nome do dispositivo aparecerá no assistente assim que for detectado. Clique em **Próximo**.

5. Insira suas credenciais de conta de Desenvolvedor da Apple e clique em **Avançar**.

6. Selecione uma equipe de desenvolvimento usando o menu suspenso para habilitar o [provisionamento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) no projeto. Clique em **Concluir**.

> [!NOTE]
> É recomendado usar o provisionamento automático para que dispositivos iOS adicionais possam ser facilmente configurados para implantação. No entanto, você poderá desabilitá-lo e continuar usando o provisionamento manual se os perfis de provisionamento corretos estiverem presentes.

## <a name="use-xamarin-hot-restart"></a>Usar o Xamarin Hot Restart
Após a configuração inicial, seu dispositivo conectado aparecerá no menu suspenso do alvo de depuração. Para depurar seu aplicativo, selecione o dispositivo na lista suspensa e clique no botão **Executar**. Talvez seja necessário ver uma mensagem no Visual Studio solicitando que você inicie manualmente o aplicativo no dispositivo para iniciar a sessão de depuração.

Você pode fazer edições a seus arquivos de código durante a depuração e pressionar o botão **Reiniciar** na barra de ferramentas de depuração ou usar **Ctrl + Shift + F5** para reiniciar a sessão de depuração com as novas alterações aplicadas:

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

Você também pode usar o `HOTRESTART` símbolo de pré-processador para impedir que determinado código seja executado durante a depuração com o Xamarin Hot restart.

## <a name="limitations"></a>Limitações

- Somente aplicativos iOS criados com Xamarin.Forms e dispositivos IOS têm suporte no momento.
- Somente dispositivos iOS de 64 bits têm suporte. A partir do iOS 11, a Apple não permite mais executar aplicativos iOS na arquitetura de 32 bits (dispositivos anteriores ao iPhone 5s).
- Não há suporte para arquivos Storyboard e XIB, e o aplicativo poderá falhar se tentar carregá-los no runtime. Use o `HOTRESTART` símbolo de pré-processador para impedir que esse código seja executado.
- Não há suporte para bibliotecas e estruturas do iOS estáticos e você poderá ver erros de tempo de execução ou falhas se seu aplicativo tentar carregá-las. Use o `HOTRESTART` símbolo de pré-processador para impedir que esse código seja executado. Há suporte para bibliotecas iOS dinâmicas.
- Não é possível usar o Xamarin Hot Restart para criar pacotes de aplicativos para publicação. Você ainda precisará de um computador Mac para fazer uma compilação completa, assinatura e implantação de seu aplicativo para produção.

## <a name="troubleshoot"></a>Solucionar problemas

- O assistente de instalação não detectará o iTunes se ele tiver sido instalado por meio da Microsoft Store. Você precisará desinstalar essa versão primeiro e, em seguida, baixar o [instalador da Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- Há um problema conhecido no qual ter builds específicos do dispositivo habilitados impede que o aplicativo entre no modo de depuração. A solução alternativa é desabilitá-lo em **Propriedades > Build do iOS** e repetir a depuração. Isso será corrigido em uma versão futura.
- Se o aplicativo já estiver presente no dispositivo, tentar implantar com o Hot Restart poderá falhar com um erro de `AMDeviceStartHouseArrestService`. A solução alternativa é desinstalar o aplicativo no dispositivo e implantá-lo novamente.
- A inserção de uma ID da Apple que não faz parte do programa de desenvolvedor da Apple pode resultar no seguinte erro: `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID` . Você deve ter uma conta de desenvolvedor da Apple válida para usar o Xamarin Hot Restart em dispositivos iOS. 

Para relatar problemas adicionais, use a ferramenta de comentários em [Ajuda > Enviar Comentários > Relatar um Problema](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
