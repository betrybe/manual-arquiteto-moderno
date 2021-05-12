# Apêndice A: Segurança {#apendice_a}

## Práticas de Segurança

Código seguro deveria ser uma regra em qualquer design de aplicação, pois em alguns ramos são tão importantes quanto qualquer regra de qualidade que se possa ter. Para microsserviços não é diferente, e aqui não existe nenhum segredo, é a mesma regra para qualquer desenho de aplicação: no mínimo se deve olhar para o [Top 10 do OWASP](https://owasp.org/www-project-top-ten/) e seguir essas recomendações no seu projeto. É essencial que profissionais que trabalham com desenvolvimento conheçam as vulnerabilidades descritas no OWASP.

Sério mesmo, não há nada novo aqui, e qualquer pessoa que esteja tentando convencer você de um "Cross Microservice Injection" ou algo do tipo está tentando te enrolar. 

No caso de Microsserviços, a abordagem que muda é em relação a Autorização e Autenticação, e vamos discutir isso mais à frente.

Uma dica importante, em se tratando de código seguro, utilize as práticas de CI/CD para validar se há alguma vulnerabilidade no seu código. É interessante confirmar se as bibliotecas de terceiros que você planeja utilizar não trarão problemas ao ambiente.

E ferramentas para isso há várias, entre elas temos o [Fortify](https://www.microfocus.com/en-us/solutions/application-security), [Snyk](https://snyk.io/), [JFrog Xray](https://jfrog.com/xray/). Porque às vezes uma dependência desatualizada pode colocar seu serviço em posição de risco, então olhar a melhor prática no código e uma ferramenta para ajudar a apontar onde melhorar formam um time imbatível.

Outra prática que eu vejo em algumas empresas, principalmente quando os serviços estão expostos apenas internarmente e não para fora, é não usar o HTTPS, ou melhor, usar um TLS (Transport Layer Security). E para que você precisa disso? Para ter privacidade, integridade e identificação.

E quando estamos falando de microsserviços, um cenário que vai acabar sempre existindo diz respeito à necessidade de falar com servidores de autorização, e podemos estar falando de um API Key, ou de um "client secret", ou até mesmo de credenciais para uma autenticação básica. Então a primeira regra básica é: não deixe essas chaves no seu repositório de fonte, esses itens precisam ser variáveis de ambientes ou chaves de configuração externa, e elas devem estar sempre encriptadas.

Como estamos falando de containers, as práticas valem também para lá, e nunca rode seu container como "root". Você precisa assumir a premissa de que seu sistema nunca é 100% seguro, alguém vai conseguir explorar algo. Então você não pode só prevenir, você precisa detectar e reagir a isso.

A chave é seguir ao menos cinco pilares:

- Segurança por design (Secure by design);
- Vasculhe suas dependências;
- Use sempre HTTPS;
- Use Tokens de Acesso e Identidade;
- Proteja e criptografe seus segredos.

#### Soluções para Autenticação e Autorização

Para o mundo de microsserviços, o principal ponto é verificar quem você é (Autenticação) e aquilo que você pode fazer (Autorização). E dentro da arquitetura de microsserviços você vai estar espalhado em muito serviços pela rede e terá que lidar com alguns problemas em relação a como resolver isso.

Autenticação e Autorização precisam ser resolvidos em cada um dos microsserviços, e parte dessa lógica global vai ter que ser replicada em todos os serviços. Neste caso, um jeito para resolver isso é criar bibliotecas para padronizar essa implementação, só que isso vai fazer que você perca um pouco da flexibilidade de quais tecnologias usar, pois a linguagem ou framework precisa suportar essa biblioteca padrão.

O uso da biblioteca também ajuda a não quebrar o princípio da responsabilidade única, já que o serviço deveria se preocupar apenas com a lógica de negócio.

E outro ponto que é preciso analisar é que os microsserviços devem ser *stateless*, então é necessário usar soluções que consigam manter isso.

Podemos abordar a Autorização e Autenticação pelo modelo de sessão distribuída, usando ferramentas para você armazenar essa sessão. Você pode abordar/manter a sessão das seguintes maneiras:

**Sticky Session** - A ideia aqui é usar o load balancer e manter as conexões de origem sempre no mesmo servidor onde veio o primeiro request. Só que esta configuração só te permite escalar horizontalmente.

**Replicação de Sessão** - Toda instância salva a sessão e sincroniza através da rede. Só que aqui isso vai causar um "overhead" de rede. Quanto mais instâncias, mais será preciso replicar e lidar com a latência disso.

**Sessão Centralizada** - Isso significa que os dados podem ser recuperados em um repositório compartilhado. Em vários cenários, esse é um ótimo desenho, porque se pode dar alto desempenho para as aplicações, e você deixa o status do login escondido dentro dessa sessão. Mas claro que existe a desvantagem de que você precisa criar mecanismos para proteger essa sessão e replicar entre as aplicações, o que pode também adicionar latências na sua rede.

Mas quando estamos neste cenário de microsserviços, a recomendação passa a ser o uso de Tokens. A maior diferença para o modelo de sessão descrito acima é que deixamos de ter algo centralizado em um servidor e passamos a responsabilidade para a própria pessoa que vai utilizá-lo.

O Token vai ter a informação de identificação da pessoa (user) que está logada, e toda vez que chega ao servidor, podemos validar no server a identidade e a autorização. O token é encriptado e pode seguir um padrão como o [JWT](https://jwt.io/).

E usando token conseguimos delegar a responsabilide do estado da pessoa (user) logada, para algum processo que possa a validade do mesmo. Habilitamos vários tipos de validações de segurança que podem ser colocadas na malha (Service Mesh) ou no seu gateway de entrada e retirar essas responsabilidades dos serviços e aplicações e mesmo assim ainda continuar garantindo a segurança.

Com o uso do JWT, você passa a ter um "client token", e você vai passar a algum servidor para que ele possa fazer a validação/criação do mesmo. 

E quando se fala em Tokens, a chave é não querer reinventar a roda, e sim usar aquilo que já está consolidadado! É onde entra o OpenId e o OAuth/OAuth2. O Oauth 2 é praticamente o padrão mais utilizado para autenticação.

O padrão de OpenID é aquele usando quando você pode se conectar ou usar o token para se logar em vários sites ou serviços. Mas no seu padrão local, a recomendação é o OAuth, que estabelece um protocolo para que você tenha acesso aos recursos de que você precisa, e ele trabalha com quatro papéis:

**Resource Owner** - Esse é o papel que controla os acessos aos recursos.

**Resource Server** - É onde ficam os serviços a serem acessados, ou seja, aqui é onde estão as suas API's, aplicações e etc.

**Client** - É quem faz a solicitação ao Resource Owner do recurso que precisa consumir.

**Authorization Server** - Quem gera os tokens de acesso, permite que o Client chegue ao recursos que foram permitidos, com o nível de acesso definido.

E como funciona isso? Basicamente o "client" solicita ao Resource Owner acesso ao recurso, e este, quando autoriza, envia para o "Client" o "authorization grant", que é a credencial que representa que o Resource Owner autorizou a passagem. Então o "Client" vai solicitar ao Authorization Server um Token de acesso; tudo sendo válido, o Client recebe o seu token de acesso, que vai ser repassado para o Resource Server para que ele possa consumir aquilo que foi solicitado.

E existem 4 tipos de fluxos para obtermos o token de acesso no padrão OAuth. Temos o Authorization Code, que é o tipo mais comum; o Implicit, que é muito utlizado por aplicações SPA; o Resource Owner, que estabelece a confiança entre as aplicações; e o Client Credentials, que é usado para falar de um serviço para outro.

Tirando a parte de Autenticação e Autorização, que precisam de um cuidado à parte, a segurança de microsserviços é como a segurança de qualquer aplicação e precisamos prestar atenção a isso.