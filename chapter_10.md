# Destrinchando performance de aplicações

## Introdução e conceitos

Muitas empresas utilizam a abordagem "deixe o desempenho para depois" para tratar com possíveis problemas de performance que possam ocorrer no ambiente de produção para a fase de testes. Na fase de testes, é provável que não se leve em consideração a carga que a aplicação possa sofrer e o tempo de resposta esperado, pois isto não foi especificado nos requisitos não funcionais. Requisitos como o tempo de resposta de uma determinada operação ou a quantidade de acessos simultâneos. Além disso, temos o desafio de conciliar uma Arquitetura evolutiva juntamente com a Engenharia de desempenho ou performance. Na arquitetura evolutiva temos que ser ágeis e permitir constantes melhorias na arquitetura, deixando as decisões para última hora, enquanto a Engenharia de desempenho se preocupa em identificar possíveis gargalos e definir as tecnologias utilizadas já no início do projeto.

Antes de irmos mais a fundo, vamos rever alguns conceitos importantes sobre performance de aplicações.

**Throughput**: É a vazão ou taxa de transferência que sua aplicação consegue dar as requisições. Número de requisições por segundo/minuto ou hora. Quanto maior este número, melhor. Geralmente nas ferramentas de teste de carga, o Throughput é medido da seguinte forma: Throughput = (número de requisições) / (tempo total).

**Latência**:  É o tempo que leva para um pacote de dados ir de um ponto a outro. Ao contrário do Throughput, quanto maior a latência, pior a performance. Em sistemas distribuídos, este pode ser um grande problema.

Sistemas de alta performance tem como requisitos, alta confiabilidade, baixa latência e alta taxa de vazão. Isso parece comum para que vai desenvolver um serviço, porém não é uma tarefa das mais fáceis em manter e melhorar a performance à medida que o sistema vai sendo utilizado, novas funcionalidades são adicionadas, base de dados crescem.

