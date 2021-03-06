Um punhado de Monads
====================

Quando nós começamos a falar sobre functors, vimos que elas foram conceitos úteis para os valores que poderiam ser mapeados. Então, nós tornamos esse conceito um passo a mais atrávez da introdução da applicative functors, o que nos permite ver certos tipos de valores como valores com contexto e utilizar funções normais nesses valores enquanto preserva-se o significado desses conceitos.

Neste capítulo, nós vamos estudar sobre monads, que apenas reforça applicative functors. Muito semelhante a applicative functors que somente reforça functors.

Quando nós começamos com functors, vimos que é possível mapear funções de vários tipos de dados. Nós vimos que para esse propósito, o tipo de classe [code]Functor[/code] nos foi apresentado o que nos levou a fazer a seguinte pergunta: quando temos um tipo de função [code]a -&gt; b[/code] e também um tipo de dado [code]f a[/code], como nós fazemos para mapear essa função para terminar com um tipo de dado [code]f b[/code]? Nós vimos como mapear somente um [code]Maybe a[/code], uma lista [code][a][/code], um [code]IO a[/code] etc. Nós até vimos como mapear uma função [code]a -&gt; b[/code] para outra função do tipo [code]r -&gt; a[/code] para obter uma função do tipo [code]r -&gt; b[/code]. Para responder essa pergunta de como mapear uma função para algum tipo de dado, tudo o que nós temos que fazer é olhar para o tipo de dado [code]fmap[/code]:

E então fizemos funcionar para nossos tipos de dados escrevendo instâncias apropriadas de [code]Functor[/code].

Em seguida vimos uma possibilidade de melhorar as functors e dizer, ei, e se essa função [code]a -&gt; b[/code] já estiver empacotada dentro de um functor value? Por exemplo, e se nós tivermos [code]Just (*3)[/code], como nós fazemos para aplicar isso a [code]Just 5[/code]? E se nós não quisermos aplicar para [code]Just 5[/code] mas em vez disso para um [code]Nothing[/code]? Ou se nós temos [code][(*2),(+4)][/code] como podemos aplicar isso a [code][1,2,3][/code]? Como isso funciona mesmo? Por isso, o tipo de classe [code]Applicative[/code] foi introduzido, no qual queremos a resposta para o seguinte tipo:

Vimos também que podemos ter um valor normal e empacota-lo dentro de um tipo de dado. Por exemplo, podemos ter [code]1[/code] e empacota-lo de modo a se tornar um [code]Just 1[/code]. Ou podemos transforma-lo em [code][1][/code]. Ou em uma ação de I/O que não faz nada e apenas produz [code]1[/code]. A função que faz isso é chamada de [code]pure[/code].

Como dissemos, um valor de aplicativo pode ser visto como um valor com um contexto adicional. Um valor <i>imaginário</i>, incluído em termos técnicos. Por exemplo, o caractere [code]'a'[/code] é apenas um caractere normal, enquanto que [code]Just 'a'[/code] tem algum contexto adicionado. Em vez de um [code]Char[/code], nós temos um [code]Maybe Char[/code], que nos diz que o valor pode ser um caractere, mas também pode ser uma ausência de um caractere.

Foi ótimo ver como o tipo de classe [code]Applicative[/code] nos permitiu usar funções normais nesses valores com contexto e como que o contexto foi preservado. Observe:

Ah, legal, agora que tratamos eles como valores de aplicativo, [code]Maybe a[/code] representa valores calculados que podem ter falhado, [code][a][/code] representa valores calculados que tem vários resultados (cálculos não determinísticos), [code]IO a[/code] são valores que representam valores que tem efeitos colaterais, etc.

Monads são extensões naturais de applicative functors e com elas estamos preocupados com isto: Se você tem um valor com um contexto, [code]m a[/code], como você aplica isto a uma função que tem um contexto normal [code]a[/code] e retorna um valor com um contexto? Quer dizer, como você aplica uma função do tipo [code]a -&gt; m b[/code] para um valor do tipo [code]m a[/code]? Assim, basicamente nós vamos querer essa função:

<b>Se você tem um valor imaginário e uma função que tem um valor normal mas retorna um valor imaginário, como fazemos para alimentar este valor imaginário em uma função?</b> Esta é a principal questão que vamos nos preocupar quando estivermos lidando com monads. Nós escrevemos [code]m a[/code] em vez de [code]f a[/code] porque o [code]m[/code] significa [code]Monad[/code], mas monads são apenas applicative functors que suportam [code]&gt;&gt;=[/code].  A função [code]&gt;&gt;=[/code] é pronunciada como <i>bind</i>.

Quando nós temos um valor normal [code]a[/code] e uma função normal [code]a -&gt; b[/code] é realmente fácil alimentar o valor da função - você apenas aplica a função com o valor que vem com determinado contexto, é preciso um pouco de pensamento para ver como esses valores imaginários são alimentados em funções e como levam em conta o seu comportamento, mas você vai ver como é fácil como um, dois três.