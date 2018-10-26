
A seguinte linha de comando para especificar um build de versão da solução **SOLUTION_FILE.sln** para iPhone. O local de IPA que pode ser definido, especificando o `IpaPackageDir` propriedade na linha de comando:

 - No Mac, usando **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

O **xbuild** comando normalmente está localizado no diretório **/Library/Frameworks/Mono.framework/Commands**.

 - No Windows, usando **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** não expandirá automaticamente `$( )` expressões passados pela linha de comando. Por esse motivo, é recomendável usar um caminho completo ao definir o `IpaPackageDir` na linha de comando.


Consulte a [notas de versão do iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) para obter mais detalhes sobre o `IpaPackageDir` propriedade.
