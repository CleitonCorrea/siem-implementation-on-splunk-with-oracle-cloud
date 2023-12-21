# siem-implementation-on-splunk-with-oracle-cloud
Implementar um sistema SIEM no Splunk usando logs transmitidos do Oracle Cloud

Um sistema SIEM (security information and event management) é uma ferramenta de operações essenciais para gerenciar a segurança de seus recursos de nuvem. O Oracle Cloud Infrastructure inclui recursos nativos de detecção, prevenção e resposta a ameaças, que podem ser aproveitados para implementar um sistema SIEM eficiente usando o Splunk.

Os administradores do Splunk Enterprise podem usar os serviços Logging e Streaming com o OCI Logging Addon para Splunk para transmitir logs de recursos na nuvem para um ambiente Splunk existente ou novo. Os administradores também podem integrar-se com outros plugins Splunk e origens de dados, como feeds de inteligência de ameaças, para aumentar a geração de alertas com base em dados de log.

Arquitetura
Nesta arquitetura, o serviço Logging captura logs do balanceador de carga e do fluxo da rede virtual na nuvem (VCN). Há um fluxo separado para cada log, e cada log é conectado ao seu fluxo com um hub de conector de serviço. Como administrador do Splunk Enterprise, você pode coletar os dados transmitidos para análise posterior usando o Logging Addon para Splunk.

O diagrama a seguir ilustra essa arquitetura de referência.

Veja a seguir a descrição da ilustração siem-logging-oci.png
Descrição da ilustração siem-logging-oci.png

Download diagram
A arquitetura tem os seguintes componentes:

Região
Uma região do Oracle Cloud Infrastructure é uma área geográfica localizada que contém um ou mais data centers, chamados domínios de disponibilidade. As regiões são independentes de outras regiões e vastas distâncias podem separá-las (entre países ou mesmo continentes).

Domínio de disponibilidade
Os domínios de disponibilidade são centros de dados independentes e independentes em uma região. Os recursos físicos em cada domínio de disponibilidade são isolados dos recursos nos outros domínios de disponibilidade, o que fornece tolerância a falhas. Os domínios de disponibilidade não compartilham infraestrutura, como energia ou resfriamento, ou a rede de domínios de disponibilidade interna. Portanto, é improvável que uma falha em um domínio de disponibilidade afete os outros domínios de disponibilidade na região.

Rede virtual na nuvem (VCN) e sub-redes
Um VCN é uma rede personalizável definida por software que você configura em uma região do Oracle Cloud Infrastructure. Como as redes tradicionais de data center, as VCNs oferecem controle total sobre seu ambiente de rede. Uma VCN pode ter vários blocos CIDR não sobrepostos que você pode alterar depois de criar a VCN. Você pode segmentar uma VCN em sub-redes, que podem ter escopo para uma região ou para um domínio de disponibilidade. Cada sub-rede consiste em uma faixa contígua de endereços que não se sobrepõem às outras sub-redes da VCN. É possível alterar o tamanho de uma sub-rede após a criação. Uma sub-rede pode ser pública ou privada.

Balanceador de carga
O serviço Oracle Cloud Infrastructure Load Balancing fornece distribuição de tráfego automatizada de um único ponto de entrada para vários servidores no backend.

Máquinas Virtuais (VMs)
O serviço Oracle Cloud Infrastructure Compute permite provisionar e gerenciar hosts de computação na nuvem. Você pode iniciar instâncias de computação com formas que atendem aos requisitos de recursos de CPU, memória, largura de banda da rede e armazenamento. Depois de criar uma instância de computação, você pode acessá-la de forma segura, reiniciá-la, anexá-la e desconectar volumes e encerrá-la quando não precisar mais dela.

Log
O log é um serviço altamente dimensionável e totalmente gerenciado que fornece acesso aos seguintes tipos de logs de seus recursos na nuvem:
Logs de Auditoria: Logs relacionados aos eventos emitidos pelo serviço de Auditoria.
Logs de serviço: Logs emitidos por serviços individuais, como Gateway de API, Eventos, Funções, Balanceamento de Carga, Armazenamento de Objetos e logs de fluxo da VCN.
Logs personalizados: Logs que contêm informações de diagnóstico de aplicativos personalizados, outros provedores de nuvem ou um ambiente local.
Streaming
O Oracle Cloud Infrastructure Streaming fornece uma solução de armazenamento totalmente gerenciada, escalável e durável para a ingestão de fluxos de dados contínuos de alto volume que você pode consumir e processar em tempo real. Você pode usar o Streaming para ingerir dados de alto volume, como logs de aplicativos, telemetria operacional, dados de fluxo de cliques na Web; ou para outros casos de uso em que os dados são produzidos e processados de forma contínua e sequencial em um modelo de mensagens de assinatura de publicação.

Conectores de serviço
O Oracle Cloud Infrastructure Service Connector Hub é uma plataforma de barramento de mensagens na nuvem que orquestra a movimentação de dados entre serviços no OCI. Você pode usá-lo para mover dados entre serviços no Oracle Cloud Infrastructure. Os dados são movidos usando conectores de serviço. Um conector de serviço especifica o serviço de origem que contém os dados a serem movidos, as tarefas a serem executadas nos dados e o serviço de destino para o qual os dados devem ser entregues quando as tarefas especificadas são concluídas.

Como registrar um complemento para Splunk
O Logging Addon para Splunk é um plug-in que ingere logs e outros dados diretamente no serviço Streaming. Você pode usar o plugin com Splunk Enterprise (versão 8.0 ou superior).

Recomendações
Usar as recomendações a seguir como ponto inicial. Seus requisitos podem ser diferentes da arquitetura descrita aqui.

VCN
Ao criar uma VCN, determine o número de blocos CIDR necessários e o tamanho de cada bloco com base no número de recursos que você planeja anexar a sub-redes na VCN. Use blocos CIDR que estejam dentro do espaço de endereço IP privado padrão.

Selecione blocos CIDR que não se sobreponham a nenhuma outra rede (no Oracle Cloud Infrastructure, seu data center local ou outro provedor de nuvem) para a qual você pretenda configurar conexões privadas.

Após criar um VCN, você pode alterar, adicionar e remover seus blocos CIDR.

Ao projetar as sub-redes, considere o fluxo de tráfego e os requisitos de segurança. Anexe todos os recursos em uma camada ou atribuição específica à mesma sub-rede, que pode servir como limite de segurança.

Versão Splunk
O complemento de registro para Splunk funciona com Python 3 no Splunk 8.0. Para versões mais baixas, a Splunk recomenda o uso de um encaminhador pesado executando o Splunk 8.0 para ingerir os dados e encaminhá-los para o indexador da versão mais baixa.

Log
Esta arquitetura captura logs do serviço Load Balancing e logs de fluxo do VCN. Cada instância de computação anexada a um VCN tem uma ou mais placas de interface de rede virtual (VNICs). Use logs de fluxo VCN para solucionar problemas de regras de segurança e para auditar o tráfego de e para as VNICs.

Controle de Acesso
O addon de log do Splunk suporta acesso por principais de instância e usando chaves de assinatura de API. A Oracle recomenda o uso de um principal de instância para evitar o armazenamento de tokens de longa duração. Se você não estiver usando um principal da instância, use uma chave de assinatura de API.

Dependendo do método de acesso escolhido, defina uma política de privilégio mínimo, conforme mostrado nos seguintes exemplos:
Se você escolher o método de acesso instance-principal: Allow dynamic-group SplunkInstance to use stream-pull in compartment <compartment>
Se você escolher o método de chave de assinatura de API: Allow group Splunk to use stream-pull in compartment <compartment>
Gateway de serviço
Se você implantar um encaminhador Splunk dentro de sua tenancy, use um gateway de serviço para se comunicar com os pontos finais do serviço Streaming.

Considerações
Ao implementar essa arquitetura, considere os seguintes fatores:

Desempenho
A arquitetura é dimensionada com base no número de eventos gerados pelo grupo de logs. O log é um serviço altamente escalável.

O streaming também é altamente escalável e é usado como um canal temporário para armazenar informações de evento enviadas do serviço de Log. Ele também atua como balanceador de carga. Considere ajustar o número de partições e fluxos, com base na quantidade de dados de log esperada.

Disponibilidade
A Oracle garante alta disponibilidade dos serviços de Streaming e Logging, que são serviços nativos em nuvem e totalmente gerenciados.

O fluxo inclui os seguintes recursos de alta disponibilidade:
Fluxo constante de dados de log
Serviço multithread e escalável horizontalmente
Ingestão quase em tempo real
Resiliência contra interrupções de curto prazo
Otimizado para uso eficiente de dados
Extensibilidade
Além dos logs de fluxo do VCN e dos logs do balanceador de carga, você pode transmitir outros logs para o Splunk usando o addon de log para o Splunk. Veja a seguir alguns exemplos:
Logs de auditoria do IAM
Logs de chamada de função
Logs de execução e acesso ao API Gateway
Logs do serviço de eventos
Considere implantar o Splunk em uma instância de computação na nuvem. A seção Explorar Mais inclui um link para a pilha do Terraform que você pode usar para provisionar o Splunk no Oracle Cloud Infrastructure.

Implantar
O código do Terraform para esta arquitetura de referência está disponível no GitHub. Você pode acessar o código no Oracle Cloud Infrastructure Resource Manager com um único clique, criar a pilha e implantá-la. Como alternativa, você pode fazer download do código do GitHub para seu computador, personalizar o código e implantar a arquitetura usando a CLI do Terraform.

Implante usando o Oracle Cloud Infrastructure Resource Manager:
Clique em Implante no Oracle Cloud
Se você ainda não estiver conectado, informe a tenancy e as credenciais do usuário.

Revise e aceite os termos e condições.
Selecione a região na qual você deseja implantar a pilha.
Siga os prompts na tela e as instruções para criar a pilha.
Após criar a pilha, clique em Ações do Terraform e selecione Planejar.
Aguarde a conclusão da tarefa e revise o plano.
Para fazer qualquer alteração, retorne à página Detalhes da Pilha, clique em Editar Pilha e faça as alterações necessárias. Em seguida, execute a ação Planejar novamente.

Se nenhuma alteração adicional for necessária, retorne à página Detalhes da Pilha, clique em Ações do Terraform e selecione Aplicar.
Implante usando a CLI do Terraform:
Vá para GitHub.
Clone ou faça download do repositório para seu computador local.
Siga as instruções no documento READM
