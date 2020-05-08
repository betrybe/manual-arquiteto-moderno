# Microservices



Microserviços são serviços ou aplicações independentes que são modeladas para o dominio de negócios (o que faz o DDD ser muito importante). Ela se comunicam entre si via protocolos de rede, e tem uma arquitetura que pode resolver problemas que você tenha.Eles são um dos tipos do SOA (service-oriented architeture), só que com o foco de colocar fronteiras bem especificas de negocios e promover a entrega independente destes serviços, outra vantagem é também pode ser agnóstico de tecnologia.Vamos resumir no ponto de vista de tecnologia, são capacidades de negócios encapsuladas em um ou mais endpoints, mas seguindos os seguintes pilares:



* Independencia para Deploy
* Modelado em volta do Dominio de Negócio
* Responsavel pelo seu próprio dado



1.  Pontos de Atenção
    

Claro que não estamos falando de uma “bala de prata” que vai resolver todos os seus problemas. E um dos principais desafios quando se migra para o cenário de microserviços é a comunicação entre os mesmos.

A comunicação entre eles não é instantânea, então um ponto de preocupação que você terá que colocar em seus desenhos e soluções será a latência. E quanto mais seu ambiente crescer em complexidade, mais isso pode se tornar um problema. Lidar com o fato de que sua rede pode e vai falhar causa muita dor de cabeça.

Outro ponto é olhar a estratégia de fazer micro-serviços apenas no backend e esquecer que o Front também é parte disso. Ou seja, deixar a sua interface como um monolito gigante não vai lhe trazer vantagem nenhuma mesmo que todo o server-side esteja distrubuído.

Umas das vantagens dos micro-serviços que é ser agnóstico de tecnologia pode se transformar no seu calvário depois. Não adianta deixar que seu ambiente seja completamente poliglota, se depois ninguém vai saber manter e operar, e ainda começar a ter problemas de latência por uma linguagem mal utilizada. É preciso ser conciso e usar tecnologias que seu time e equipes de suporte dominem.

E aquela máxima então, que todos já devem ter ouvido: “Mas qual é o tamanho que o microserviço precisa ter?”. E a maioria das pessoas confunde o “Micro” com o tamanho e acha que ele precisa ser do menor tamanho possível, acha que realmente tem relação com a quantidade de bytes que ele ocupa.

Mas como é medido esse tamanho? Linhas de código? Tamanho em bytes? Nada disso, é o principio da responsabilidade única, esse é o tamanho que você precisa respeitar.

Outro ponto de atenção importante é quem será o dono do microserviço, pode até parecer bobeira, mas em ambientes altamente distribuídos, um serviço que está rodando em produção e não apresenta problema pode acabar ficando sem um dono e na hora de um problema ou possível manutenção, fica dificil achar quem é que assume a responsabilidade, então é preciso ter processos de governança bem fortes.

  

2.  Vantagens e desvantagens em relação ao monolito