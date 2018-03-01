
Ao criar aplicativos iOS no Visual Studio e o agente de build do Mac, o pacote do .app não será copiado de novo no computador Windows. As ferramentas Xamarin para Visual Studio 7.4 adicionam uma nova propriedade `CopyAppBundle` que permite que builds de CI copiem pacotes do .app para o Windows.

Para usar essa funcionalidade, adicione a propriedade `CopyAppBundle` ao .csproj no grupo de propriedades para o qual você deseja aplicar essa funcionalidade. Por exemplo, o exemplo a seguir mostra como copiar o pacote do .app de volta para o computador Windows para um build de **Depuração** direcionado para o **iPhoneSimulator**:

    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
        <CopyAppBundle>true</CopyAppBundle>
    </PropertyGroup>

