# [Sumário](./index.md) > Declaração de Variáveis

~~~javascript
data() {
    return {
        pagina: 1,                  // 1
        qtdePaginas: 1,             // 2
        limite: 10,                 // 3
        limites: [ 10, 25, 50 ],    // 4 	
        listaItens: [],             // 5    
        listaItensPaginado: [],     // 6
        paginaNavegacao: [],        // 7
        paginaEscolhida: null,      // 8
	}
}
~~~

### Legenda:

- (1) página corrente 
- (2) qtde total de páginas
- (3) limite de itens por página
- (4) controla o valor de 'limite'
- (5) lista de dados que serão exibidos. Este item deve receber a lista principal de dados.
- (6) lista resultante dos cálculos de controle de paginação. É essa lista que será exibida.
- (7) lista auxiliar para o controle de paginação
- (8) página escolhida via INPUT

# Métodos 
~~~javascript
methods: {
    OrganizarNavegacao() {
        if (this.pagina == '...' || this.pagina > this.qtdePaginas)
            return;
        
        this.pagina = parseInt(this.pagina);

        if (isNaN(this.pagina))
            return;

        this.paginaNavegacao = [];

        if (this.pagina > 1) {
            let paginasAnteriores = this.pagina - 2;

            for (let z = paginasAnteriores; z < this.pagina; z++) {
                if (z > 0 && z != this.pagina)
                    this.paginaNavegacao.push(z);
            }
        }

        this.paginaNavegacao.push(this.pagina);

        if (this.pagina != this.qtdePaginas) {
            let paginasPosteriores = this.pagina + 2;

            for (let x = this.pagina; x < paginasPosteriores; x++) {
                if (x != this.pagina)
                    this.paginaNavegacao.push(x);
            }

            if (this.pagina - 3 < this.qtdePaginas - 5) {
                this.paginaNavegacao.push('...');
                this.paginaNavegacao.push(this.qtdePaginas);
            }
        }
    },

    Paginacao(itens) {
        let pagina = this.pagina;
        let qtdeLinhas = this.limite;
        let de = (pagina * qtdeLinhas) - qtdeLinhas;
        let para = (pagina * qtdeLinhas);

        this.listaItensPaginado = itens.slice(de, para);
        
        return this.listaItensPaginado;
    },

    PaginarTabela() {
        let tamanhoTotal = this.listaItens.length;
        let qtdeLinhas = this.limite;
        let totalLinhas = tamanhoTotal;
        let paginas = Math.ceil(totalLinhas / qtdeLinhas);
        
        this.qtdePaginas = 0;
        
        for (let i = 0; i < paginas; i++) {
            this.qtdePaginas++;
        }

        this.OrganizarNavegacao();
    },
},
~~~

# Métodos Computados
~~~javascript
computed: {
    Paginar() {
        if( this.listaItens ) {
            return this.Paginacao( this.listaItens );
        }
        return [];
    },
},
~~~

# Controle Watch
~~~javascript
watch: {
    // Verificar se a pagina é menor que a quantidade total, ou se a página é igua a '...'
    pagina: function (newPage, lastPage) {
        if (newPage == '...' || newPage > this.qtdePaginas)
            this.pagina = lastPage;
    },
},
~~~

# Aplicação HTML

## Requisitos
- Font Awesome: 
~~~javascript 
<script src="https://kit.fontawesome.com/09825d3342.js" crossorigin="anonymous"></script> 
~~~

- Bootstrap CSS:
~~~javascript
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
~~~

## CSS Adicional
~~~css
.valor {
    border: #ddd solid 1px;
    float: left;
    cursor: text;
}
~~~

## Aplicação do SELETOR da qtde de itens por página (Topo da tabela)
~~~html
<div class="col-md-1 float-right mb-1 mt-2">
    <input type="number" class="form-control form-control-sm valor" placeholder="Página" v-model="paginaEscolhida" v-on:keyup.enter="pagina = paginaEscolhida;OrganizarNavegacao()" />

    <div id="lupa" v-on:click="pagina = paginaEscolhida;OrganizarNavegacao()">
        <i class="fa fa-search lupa"></i>
    </div>
</div>

<div class="col-md-1 float-right mb-1 mt-2">
    <select class="form-control form-control-sm" v-model="limite" title="Altera o limite de linhas por página." v-on:change="pagina=1;PaginarTabela()">
        <option v-for="(item, i) in limites" v-bind:value="item">{{ item }}</option>
    </select>
</div>

<div class="col-md-1 float-right mb-1 mt-3">
    <span>Itens por página:</span>
</div>
~~~

## Aplicação da paginação na lista
~~~html
<template v-for="(i, index) in Paginar" v-bind:key="index">
    <tr>
        <td class="text-center"> {{ i.campo_01 }}</td>
        <td class="text-center"> {{ i.campo_02 }}</td>
        <td class="text-center"> {{ i.campo_03 }}</td>
        <td class="text-center"> {{ i.campo_04 }}</td>
    </tr>
</template>
~~~

## Aplicação da NAVEGADOR da paginação (Rodapé da tabela)
~~~html
<template v-if="qtdePaginas > 1">
    <div class="col-md-5 col-lg-7 mb-1 mr-0 mt-3">
        <nav class="d-lg-flex justify-content-end overflow-auto" aria-label="Page navigation example">
            <ul class="pagination">

                <li class="page-item" v-if="pagina != 1">
                    <a class="page-link" v-on:click.prevent.stop="pagina = 1;OrganizarNavegacao()" href="#">
                        <i class="fas fa-angle-double-left"></i>
                    </a>
                </li>

                <li class="page-item" v-if="pagina != 1">
                    <a class="page-link" v-on:click.prevent.stop="pagina--;OrganizarNavegacao()" href="#">
                        <i class="fas fa-angle-left"></i>
                    </a>
                </li>

                <li v-for="item in paginaNavegacao" class="page-item" :class="{active: (item) === pagina}" v-on:click.prevent.stop="pagina = item;OrganizarNavegacao()">
                    <a class="page-link" href="#">{{item}}</a>
                </li>

                <li class="page-item" v-if="pagina < qtdePaginas" v-on:click.prevent.stop="pagina++;OrganizarNavegacao()">
                    <a class="page-link" href="#">
                        <i class="fas fa-angle-right"></i>
                    </a>
                </li>

                <li class="page-item" v-if="qtdePaginas > 1 && pagina < qtdePaginas" v-on:click.prevent.stop="pagina = qtdePaginas;OrganizarNavegacao()">
                    <a class="page-link" href="#">
                        <i class="fas fa-angle-double-right"></i>
                    </a>
                </li>

            </ul>
        </nav>
    </div>
</template>
~~~
