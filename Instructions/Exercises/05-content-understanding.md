---
lab:
  title: "Analisar conteúdo com\_a Compreensão de Conteúdo da IA do Azure"
  module: Multimodal analysis with Content Understanding
---

# Analisar conteúdo com a Compreensão de Conteúdo da IA do Azure

Neste exercício, você usará o Portal da Fábrica de IA do Azure para criar um projeto de compreensão de conteúdo que pode extrair informações de faturas. Em seguida, você testará seu analisador de conteúdo no Portal da Fábrica de IA do Azure e o consumirá por meio da interface REST de compreensão de conteúdo.

Este exercício levará aproximadamente **30** minutos.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, digite um nome válido para o seu projeto e, se um hub existente for sugerido, escolha a opção para criar um novo. Em seguida, examine os recursos do Azure que serão criados automaticamente para dar suporte ao hub e ao projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome válido para o seu hub*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Local**: selecione uma das seguintes regiões\*
        - Oeste dos EUA
        - Suécia Central
        - Leste da Austrália
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure**: *Criar um novo recurso de Serviços de IA*
    - **Conectar a Pesquisa de IA do Azure**: *crie um novo recurso da Pesquisa de IA do Azure com um nome exclusivo*

    > \*No momento da redação deste artigo, a Compreensão de Conteúdo da IA do Azure está disponível apenas nessas regiões.

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](../media/ai-foundry-project.png)

## Criar um analisador de Compreensão de Conteúdo

Você vai construir um analisador que pode extrair informações de faturas. Você começará definindo um esquema com base em uma fatura de exemplo.

1. Em uma nova guia do navegador, baixe o formulário de exemplo [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) de `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` e salve-o em uma pasta local.
1. Retorne à guia que contém a home page do seu projeto da Fábrica de IA do Azure e, no painel de navegação à esquerda, selecione **Compreensão de Conteúdo**.
1. Na página **Compreensão de Conteúdo**, selecione a guia **Analisador personalizado** na parte superior.
1. Na página do analisador personalizado da Compreensão de Conteúdo, selecione **+ Criar** e crie uma tarefa com as seguintes configurações:
    - **Nome da tarefa**: análise de fatura
    - **Descrição**: extrair dados de uma fatura
    - **Conexão dos Serviços de IA do Azure**: *o recurso de Serviços de IA do Azure em seu hub da Fábrica de IA do Azure*
    - **Conta de Armazenamento de Blobs do Azure**: *a conta de armazenamento padrão no hub da Fábrica de IA do Azure*
1. Aguarde pela tarefa a ser criada.

    > **Dica**: se ocorrer um erro ao acessar o armazenamento, aguarde um minuto e tente novamente.

1. Na página **Definir esquema**, carregue o arquivo **invoice-1234.pdf** que você acabou de baixar.
1. Escolha o modelo de **Análise de fatura** e selecione **Criar**.

    O modelo de *análise de fatura* inclui campos comuns encontrados em faturas. É possível usar o editor de esquema para excluir qualquer um dos campos sugeridos que você não precisa e adicionar os campos personalizados necessários.

1. Na lista de campos sugeridos, selecione **BillingAddress**. Este campo não é necessário para o formato de fatura que você enviou, portanto, use o ícone **Excluir campo** (**&#128465;**) exibido para excluí-lo.
1. Agora, exclua os seguintes campos sugeridos, que não são necessários:
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - CustomerId
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - RemittanceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiscount
    - VendorAddressRecipient
    - VendorTaxId
    - TaxDetails
1. Use o botão **+ Adicionar novo campo** para adicionar os seguintes campos:

    | Nome do campo | Descrição do campo | Tipo de valor | Método |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | String | Extração |
    | `ShippingFee` | `Fee for shipping` | Número | Extração |

1. Verifique se o esquema concluído tem esta aparência e selecione **Salvar**.
    ![Captura de tela de um esquema para uma fatura.](../media/invoice-schema.png)

1. Na página **Analisador de Teste**, se a análise não começar automaticamente, selecione **Executar análise**. Em seguida, aguarde a conclusão da análise e revise os valores de texto na fatura identificados como correspondentes aos campos no esquema.
1. Revise os resultados da análise, que devem ser semelhantes a estes:

    ![Captura de tela dos resultados do teste do analisador.](../media/analysis-results.png)

1. Exiba os detalhes dos campos que foram identificados no painel **Campos** e então exiba a guia **Resultado** para ver a representação JSON.

## Criar e testar um analisador

Agora que você treinou um modelo para extrair campos de faturas, pode criar um analisador para usar com formulários semelhantes.

1. Escolha a página **Criar analisador**, selecione **+ Criar analisador** e construa um novo analisador com as seguintes propriedades (inseridas exatamente como exibido aqui):
    - **Nome**: `contoso-invoice-analyzer`
    - **Descrição**: `Contoso invoice analyzer`
1. Aguarde até que o novo analisador esteja pronto (use o botão **Atualizar** para verificar).
1. Baixe [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) em `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf` e salve-a em uma pasta local.
1. Retorne à página **Criar analisador** e selecione o link **contoso-invoice-analyzer**. Os campos definidos no esquema do analisador serão exibidos.
1. Na página **contoso-invoice-analyzer**, selecione a guia **Teste**.
1. Use o botão **+ Carregar arquivos de teste** para enviar **invoice-1235.pdf** e executar a análise para extrair dados de campo do formulário de teste.
1. Revise os resultados do teste e verifique se o analisador extraiu os campos corretos da fatura de teste.
1. Feche a página **contoso-invoice-analyzer***.

## Use a API REST de compreensão de conteúdo

Agora que você criou um analisador, pode consumi-lo em um aplicativo cliente por meio da API REST de Compreensão de conteúdo.

1. Na área **Detalhes do projeto**, observe a **Cadeia de conexão do projeto**. Você usará essa cadeia de conexão para se conectar ao seu projeto em um aplicativo cliente.
1. Abra uma nova guia do navegador (mantendo o portal da Fábrica de IA do Azure aberto na guia existente). Em seguida, na nova guia, navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`; efetue login com suas credenciais do Azure, se solicitado.

    Feche todas as notificações de boas-vindas para ver a home page do portal do Azure.

1. Use o botão **[\>_]** à direita da barra de pesquisa na parte superior da página para criar um Cloud Shell no portal do Azure selecionando um ambiente do ***PowerShell*** sem armazenamento em sua assinatura.

    O Cloud Shell fornece uma interface de linha de comando em um painel na parte inferior do portal do Azure. Você pode redimensionar ou maximizar esse painel para facilitar o trabalho.

    > **Observação**: se você já criou um Cloud Shell que usa um ambiente *Bash*, alterne-o para o ***PowerShell***.

1. Na barra de ferramentas do Cloud Shell, no menu **Configurações**, selecione **Ir para a versão clássica** (isso é necessário para usar o editor de código).

    **<font color="red">Verifique se você mudou para a versão clássica do Cloud Shell antes de continuar.</font>**

1. No painel do Cloud Shell, insira os seguintes comandos para clonar o repositório GitHub que contém os arquivos de código para este exercício (digite o comando ou copie-o para a área de transferência e clique com o botão direito do mouse na linha de comando e cole como texto sem formatação):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **Dica**: ao colar comandos no Cloud Shell, a saída poderá ocupar uma grande quantidade do buffer da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

1. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo:

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar as bibliotecas que você usará:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. Digite o seguinte comando para editar o arquivo de configuração que foi fornecido:

    ```
   code .env
    ```

    O arquivo é aberto em um editor de código.

1. No arquivo de código, substitua o espaço reservado **YOUR_PROJECT_CONNECTION_STRING** pela cadeia de conexão do projeto (copiada da página **Visão geral** do projeto no Portal da Fábrica de IA do Azure) e verifique se **ANALISADOR** está definido para o nome atribuído ao analisador (que deve ser *contoso-invoice-analyzer*)
1. Depois de substituir os espaços reservados, no editor de código, use o comando **CTRL+S** para salvar as alterações e então use **CTRL+Q** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

1. Na linha de comando do Cloud Shell, insira o seguinte comando para editar o arquivo de código Python **analyze_invoice.py** que foi fornecido:

    ```
    code analyze_invoice.py
    ```
    O arquivo de código Python é aberto em um editor de código:

1. Revise o código, que:
    - Identifica o arquivo de fatura a ser analisado, com um padrão de **invoice-1236.pdf**.
    - Recupera o ponto de extremidade e a chave para o recurso Serviços de IA do Azure do projeto.
    - Envia uma solicitação HTTP POST para o ponto de extremidade de Compreensão de conteúdo, instruindo o a analisar a imagem.
    - Verifica a resposta da operação POST para recuperar uma ID para a operação de análise.
    - Envia repetidamente uma solicitação HTTP GET para o serviço de Compreensão de conteúdo até que a operação não esteja mais em execução.
    - Se a operação tiver sido bem-sucedida, analisa a resposta JSON e exibe os valores recuperados
1. Use o comando **CTRL+Q** para fechar o editor de código enquanto mantém a linha de comando do Cloud Shell aberta.
1. No painel de linha de comando do Cloud Shell, insira o seguinte comando para executar o código Python:

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. Examine a saída do programa.
1. Use o seguinte comando para executar o programa com uma fatura diferente:

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **Dica**: há três arquivos de fatura na pasta de código que você pode usar (invoice-1234.pdf, invoice-1235.pdf e invoice-1236.pdf) 

## Limpar

Se tiver terminado de usar o serviço Compreensão de conteúdo, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. No Portal da Fábrica de IA do Azure, navegue até o projeto **travel-insurance** e exclua-o.
1. No portal do Azure, exclua o grupo de recursos criado para este exercício.

