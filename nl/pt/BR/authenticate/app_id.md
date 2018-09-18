---

copyright:
  years: 2018
lastupdated: "2018-08-09"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Incluindo Autenticação do Usuário

A segurança do aplicativo pode ser muito complicada. Para a maioria dos desenvolvedores, é uma das partes mais difíceis da criação de um app. Como é possível ter certeza de que você está protegendo suas informações sobre o usuário? Ao integrar o {{site.data.keyword.appid_full}} aos apps, é possível proteger recursos e incluir autenticação; mesmo quando não se tem muita experiência em segurança.

Ao exigir que os usuários se conectem, é possível armazenar dados do usuário, como preferências do app (ou informações de perfis sociais públicos) e, em seguida, alavancar esses dados para customizar a experiência de cada usuário dentro do app. O {{site.data.keyword.appid_short_notm}} fornece uma estrutura de login para você, mas também é possível trazer suas próprias telas de conexão de marca para usar com o diretório da nuvem.

Para obter todas as maneiras de uso do {{site.data.keyword.appid_short_notm}} e informações de arquitetura, consulte [Sobre o {{site.data.keyword.appid_short_notm}}](/docs/services/appid/about.html).

## Antes de começar

Primeiro, verifique se os pré-requisitos a seguir estão prontos para execução:
 * CocoaPods (versão 1.1.0 ou superior)
 * iOS (versão 9 ou superior)
 * MacOS (versão 10.11.5 ou superior)
 * Xcode (versão 9.0.1 ou superior)

## Etapa 1. Criando uma instância do  {{site.data.keyword.appid_short_notm}}

Provisem uma instância do serviço:

1. No  [ {{site.data.keyword.cloud_notm}}  catálogo ](https://console.bluemix.net/catalog/), selecione  {{site.data.keyword.appid_short_notm}}. A tela de configuração de serviço é aberta.
2. Dê um nome à sua instância de serviço ou use o nome de pré-configuração.
3. Selecione o seu plano de precificação e clique em **Criar**.

## Etapa 2. Instalando o SDK do iOS Swift

O serviço fornece um SDK para ajudar a facilitar a codificação do app. O SDK deve ser instalado em seu código de app.

1. Abra o diretório de projeto do Xcode existente para o `Podfile`.

2. No destino de seus projetos, inclua uma dependência para o pod `BluemixAppID`. Assegure-se de que `use_frameworks!` também está sob o seu destino, conforme mostrado no exemplo a seguir.
    ```swift
    target '<yourTarget>' do
      use_frameworks!
        pod 'BluemixAppID'
    end
    ```
    {: pre}

3. Faça download da dependência  ` BluemixAppID ` .
    ```
    pod install -- repo-update
    ```
    {: pre}

## Etapa 3. Inicializando o SDK

Depois de inicializar o SDK em seu app, é possível iniciar a configuração de suas preferências do {{site.data.keyword.appid_short_notm}}.

1. Acesse **Configurações do projeto > Recursos > Compartilhamento de keychain** e ative o compartilhamento de keychain no projeto Xcode.

2. Acesse **Configurações do Projeto > Informações > Tipos de URL** e inclua o valor a seguir nas caixas de texto **Esquema de URL** e **Identificador**.
    ```
    $(PRODUCT_BUNDLE_IDENTIFIER)
    ```
    {: pre}

3. Inclua a importação a seguir em seu arquivo `AppDelegate.swift`.
    ```swift
    import BluemixAppID
    ```
    {:pre}

4. Passe o ID do locatário e os parâmetros de região para inicializar o SDK. Um local comum, embora não obrigatório, para colocar o código é no método `application:didFinishLaunchingWithOptions` do AppDelegate em seu app.
    ```swift
    AppID.sharedInstance.initialize(tenantId: <tenantId>, bluemixRegion: <AppID_region>)
    ```
    {: pre}
  <table>
    <thead>
      <th colspan=2><img src="images/idea.png" alt=""/>  Entendendo os Componentes de Comandos </th>
    </thead>
    <tbody>
      <tr>
        <td><em> tenantID </em></td>
        <td>O ID do locatário é um identificador exclusivo usado para inicializar o aplicativo. É possível localizar o valor no painel do {{site.data.keyword.appid_short_notm}}. Na guia <b>Credenciais de serviço</b>, clique em <b>Visualizar credenciais</b>.</td>
      </tr>
      <tr>
        <td><em> AppID_region </em></td>
        <td>A região do {{site.data.keyword.appid_short_notm}} é a região do IBM Cloud na qual você está trabalhando com o serviço. Esta região pode ser localizada no painel de serviço e pode ser <em>AppID.REGION_US_SOUTH</em>, <em>AppID.REGION_SYDNEY</em> e <em>AppID.REGION_UK</em>.</td>
      </tr>
    </tbody>
  </table>

5. Inclua o código a seguir em seu arquivo AppDelegate.
    ```swift
    func application(_ application: UIApplication, open url: URL, options :[UIApplicationOpenURLOptionsKey : Any]) -> Bool {
            return AppID.sharedInstance.application(application, open: url, options: options)
        }
    ```
    {: pre}

## Etapa 4. Gerenciando a experiência de conexão
{: #managing-signin-appid}

Um provedor de identidade fornece as informações sobre autenticação para seus usuários para que seja possível autorizá-los. Com o {{site.data.keyword.appid_short_notm}}, é possível usar provedores de identidade social, como o Facebook e o Google+, ou gerenciar um registro de usuário com o diretório da nuvem.

É possível atualizar suas configurações a qualquer momento sem atualizar o código do app.
{: tip}


### Provedores de identidade social

Com o {{site.data.keyword.appid_short_notm}}, é possível usar provedores de identidade social, como o Facebook e o Google+, para proteger seus apps.

Para configurar provedores de identidade social:

1. Abra o painel do {{site.data.keyword.appid_short_notm}} para **Provedores de identidade > Gerenciar**.
2. Configure os provedores de identidade que você deseja usar como **Ativados**. Será possível usar qualquer combinação de provedores de identidade, mas se quiser trazer telas de conexão customizadas, será necessário ativar apenas o Cloud Directory.
3. Atualize a [configuração padrão](/docs/services/appid/identity-providers.html) com suas próprias credenciais. O {{site.data.keyword.appid_short_notm}} fornece credenciais da IBM que podem ser usadas para experimentar o serviço, mas antes de publicar seu app, é necessário atualizar a configuração.
4. Customize a tela de conexão pré-configurada para exibir a imagem e as cores de sua escolha.
5. Para chamar o widget de login com seu app, inclua o comando a seguir no código.
    ```swift
    import BluemixAppID
    class delegate : AuthorizationDelegate {
        public func onAuthorizationSuccess(accessToken: AccessToken, identityToken: IdentityToken, response:Response?) {
            //User authenticated
        }

        public func onAuthorizationCanceled() {
            //Authentication canceled by the user
        }

        public func onAuthorizationFailure(error: AuthorizationError) {
            //Exception occurred
        }
    }

    AppID.sharedInstance.loginWidget?.launch(delegate: delegate())
    ```
    {: codeblock}


### Diretório da nuvem

Com o {{site.data.keyword.appid_short_notm}}, é possível gerenciar seu próprio registro de usuário chamado diretório da nuvem. O diretório da nuvem permite que os usuários se conectem e se inscrevam nos apps móveis e da web usando seu e-mail e uma senha.

Para trazer suas próprias telas de marca da IU, apenas o diretório da nuvem pode ser ativado como um provedor de identidade.
{: tip}

Para configurar o diretório de nuvem:

1. Abra o painel do {{site.data.keyword.appid_short_notm}} na guia **Gerenciando provedores de identidade** e configure o diretório da nuvem como **Ativado**.
2. Configure seu  [ diretório e configurações de mensagem ](/docs/services/appid/cloud-drectory.html).
4. Escolha as combinações de telas de conexão que gostaria de exibir e coloque o código para chamar essas telas no aplicativo.
    * Efetuar sign in
        ```swift
        class delegate : TokenResponseDelegate {
            public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
            //User authenticated
            }

            public func onAuthorizationFailure(error: AuthorizationError) {
            //Exception occurred
            }
        }

        AppID.sharedInstance.obtainTokensWithROP(username: username, password: password, delegate: delegate())
        ```
        {: codeblock}
    * Inscrever
        ```swift
        class delegate : AuthorizationDelegate {
          public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             if accessToken == nil && identityToken == nil {
              //email verification is required
              return
             }
           //User authenticated
          }

          public func onAuthorizationCanceled() {
              //Sign up canceled by the user
          }

          public func onAuthorizationFailure(error: AuthorizationError) {
              //Exception occurred
          }
        }

        AppID.sharedInstance.loginWidget?.launchSignUp(delegate: delegate())
        ```
        {: codeblock}

    * Esqueceu a senha
        ```swift
        class delegate : AuthorizationDelegate {
           public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
              //forgot password finished, in this case accessToken and identityToken will be null.
           }

           public func onAuthorizationCanceled() {
               //forgot password canceled by the user
           }

           public func onAuthorizationFailure(error: AuthorizationError) {
               //Exception occurred
           }
        }

        AppID.sharedInstance.loginWidget?.launchForgotPassword(delegate: delegate())
        ```
        {: codeblock}

    * Detalhes da conta
        ```swift

         class delegate : AuthorizationDelegate {
             public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             }

             public func onAuthorizationCanceled() {
             }

             public func onAuthorizationFailure(error: AuthorizationError) {
             }
         }

         AppID.sharedInstance.loginWidget?.launchChangeDetails(delegate: delegate())
        ```
        {: codeblock}

    * Alterar senha
        ```swift
         class delegate : AuthorizationDelegate {
             public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             }

             public func onAuthorizationCanceled() {
             }

             public func onAuthorizationFailure(error: AuthorizationError) {
             }
          }

          AppID.sharedInstance.loginWidget?.launchChangePassword(delegate: delegate())
        ```
        {: codeblock}


## Etapa 5. Testando seu aplicativo
{: #appid_testing}

Está tudo acertado corretamente? Você pode testá-lo!

1. Abra seu app com o emulador Xcode.
2. Usando a GUI, percorra o processo de assinatura em seu aplicativo. Se tiver configurado o diretório da nuvem, certifique-se de que todas as telas estejam sendo exibidas da forma desejada.
3. Atualize os provedores de identidade ou a tela do widget de login no painel do {{site.data.keyword.appid_short_notm}}.
4. Repita as etapas 1 e 2 para ver se as mudanças são implementadas imediatamente. Não são necessárias atualizações para o código do app.

Tendo problemas? Efetue o registro de saída de  [ resolução de problemas  {{site.data.keyword.appid_short_notm}} ](/docs/services/appid/ts_index.html).

## Próximas etapas
{: #appid_next}

Ótimo trabalho! Você incluiu um nível de segurança no app. Tente uma das opções a seguir para manter o ritmo:

* Obtenha mais informações e aproveite todos os recursos que o {{site.data.keyword.appid_short_notm}} tem para oferecer; [verifique os documentos](/docs/services/appid/index.html)!
* Os Starter Kits são uma das maneiras mais rápidas de alavancar os recursos do IBM Cloud. Visualize os kits do iniciador disponíveis no [Painel do desenvolvedor do dispositivo móvel](https://console.bluemix.net/developer/mobile/dashboard). Faça download do código. Execute o app.