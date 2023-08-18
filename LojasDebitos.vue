<template>
    <!-- component -->
<div class="bg-white dark:bg-gray-900">
            <div class="container px-6 py-8 mx-auto">
                <h1 class="text-3xl font-semibold text-center text-gray-800 capitalize lg:text-4xl dark:text-white">Meus Debitos:  {{ contador }} </h1>
                
                <p class="max-w-2xl mx-auto mt-4 text-center text-gray-500 xl:mt-6 dark:text-gray-300">
                    Selecione a loja para ver os seus débitos em aberto nas lojas planeta e fique sempre atualizado dos proximos vencimentos
                </p>
                
                <div class="grid grid-cols-1 gap-8 mt-6 lg:grid-cols-3 xl:mt-12">

                    <div v-for="(debito, index) in DebitosFilial" :key="index" 
                            :class="[
                                    'flex', 'items-center', 'justify-between', 'px-8', 'py-4',
                                    'border', 'cursor-pointer', 'rounded-xl',
                                    debito.selecionado ? 'border-red-500' : 'border-gray-500',
                                      ]" 
                                @click="selecionarItem(debito)">
                        <div class="flex flex-col items-center space-y-1">
                            <svg xmlns="http://www.w3.org/2000/svg"
                             :class="['w-5' ,'h-5', 'sm:h-7', 'sm:w-7', debito.selecionado ? 'text-red-600 dark:text-red-500' : 'text-gray-500 dark:text-red-500' ]"
                                 viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
                            </svg>

                            <h2 class="text-lg font-medium text-gray-700 sm:text-xl dark:text-gray-200">{{ `Loja `+ debito.FilOrig }}</h2>
                        </div>
                        
                        
                        <div class="flex flex-col items-center space-y-1">
                            <div class="px-2 text-xs text-red-500 bg-gray-100 rounded-full dark:text-blue-400 sm:px-4 sm:py-1 dark:bg-gray-700 ">
                                Desconto 30%....
                            </div>

                            <h2 :class="['text-2xl', 'font-semibold' ,'dark:text-red-500' ,'sm:text-3xl', debito.selecionado ? 'text-black-600' : 'text-gray-500'  ]">
                                
                                R$ {{debito.VremDia + debito.VrAtraso }} </h2>
                        </div>
                    </div>
                </div>
                <div class="p-2 mt-8 space-y-4 dark:bg-gray-800 rounded-xl">
                    <div class="flex items-center justify-between text-gray-800 dark:text-gray-200">
                        <p class="textlg sm:text-xs">Parcela...</p>
                        <p class="textlg sm:text-xs">Vencimento</p>
                        <p class="textlg sm:text-xs">Valor</p>

                        <div xmlns="http://www.w3.org/2000/svg" class="w-5 h-5 text-blue-500 sm:h-7 sm:w-7" viewBox="0 0 20 20" fill="currentColor">
                        
                        </div>
                    </div>
                </div>

                <div class="p-8 mt-1 space-y-8 bg-gray-100 dark:bg-gray-800 rounded-xl">
                    <div v-for="(titulos, index) in TitulosFilial" :key="index" class="flex items-center justify-between text-gray-800 dark:text-gray-200 hover:scale-105">
                        <p class="textlg sm:text-xl">{{titulos.Parcela}}</p>
                        <div class="bg-red-100 rounded-lg"><p class="textlg sm:text-xl">{{titulos.Venc}}</p></div>
                        <p class="textlg sm:text-xl">R${{titulos.Valor}}</p>

                        <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5 text-red-500 sm:h-7 sm:w-7" viewBox="0 0 20 20" fill="currentColor">
                            <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
                        </svg>
                    </div>
                </div>
                
                
                <div class="flex justify-center mt-8">
                    <button  @click="increment" class="px-8 py-2 tracking-wide text-white capitalize transition-colors duration-200 transform bg-red-600 rounded-md hover:bg-red-500 focus:outline-none focus:bg-red-500 focus:ring focus:ring-red-300 focus:ring-opacity-80">
                        Gerar PIX 
                    </button>
                </div>
            </div>
        </div>
</template>

<script>
export default {
    data() {
      return {
        contador: 0,
      };
    },

props: {
    DebitosFilial: Array,
    TitulosFilial: Array
},

  mounted() {
    this.DebitosFilialReativos = this.DebitosFilial.map(debito => ({ ...debito }));
    this.TitulosFilialReativos = this.TitulosFilial.map(titulo => ({ ...titulo }));
  },

  computed: {
    debitosFiliaisReativos() {
      return this.DebitosFilial.map(debito => ({
        ...debito,
        selecionado: false, // Inicialmente nenhum selecionado
      }));
    },
    titulosFiliaisSelecionados() {
      const debitoSelecionado = this.DebitosFilial.find(debito => debito.selecionado);
      if (debitoSelecionado) {
        return this.TitulosFilial.filter(titulo => titulo.FilOrig === debitoSelecionado.FilOrig);
      } else {
        return []; // Retorna um array vazio se nenhuma loja estiver selecionada
      }
    },
},

methods: {
        formatLoja(nomeLoja) {
            // Remove os números e mantém apenas as letras maiúsculas iniciais nas palavras
            const words = nomeLoja.split(' ');
            const formattedWords = words
                .filter(word => isNaN(word))
                .map(word => `${word.charAt(0).toUpperCase()}${word.slice(1).toLowerCase()}`)
                .join(' ');

            return formattedWords;
        },
        increment() {
        this.contador++;
      },
        atraso(vencimento){

            const now = moment(new
             Date()); // Data de hoje
            const past = moment(vencimento); // Outra data no passado
            const duration = moment.duration(now.diff(past));

            // Mostra a diferença em dias
            const days = duration.asDays();

            return days;

        },
        selecionarItem(debitoClicado) {
              this.DebitosFilial.forEach((debito, index) => {
                  this.$set(this.DebitosFilial, index, { ...debito, selecionado: debito === debitoClicado });
                 });
        },
  },


  name: 'LojasDebitos2',
}

</script>

