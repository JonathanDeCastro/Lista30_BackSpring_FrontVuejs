<template>
<div id="app">
    <nav>
      <div class="nav-wrapper blue darken-1">
        <a href="#" class="brand-logo center"><em>Front-end</em> de Funcionários</a>
      </div>
    </nav>

    <div class="container">
      <form @submit.prevent="desabilitado ? incluir() : salvar()">
        <label>ID</label> 
        <input disabled name="nID" type="number" placeholder="ID" v-model="funcionario.id">
        
        <label>Nome</label> 
        <input :disabled=desabilitado type="text" name="nNome" placeholder="Nome" v-model="funcionario.nome" required/>

        <label># Dependentes</label>
        <input :disabled=desabilitado type="number" name="nNumDep" placeholder="# Dependentes" v-model="funcionario.numDep" required/>

        <label>Salário</label>
        <input :disabled=desabilitado type="number" name="nSalario" placeholder="Salário" step="any" v-model="funcionario.salario" required/>
        
        <label>Data de nascimento</label>
        <input :disabled=desabilitado type="date" name="nNascimento" placeholder="Data de Nascimento" step="any" v-model="funcionario.nascimento" required/>

        <button type="submit" class="waves-effect waves-light btn-small">
          {{ desabilitado ? 'Incluir' : 'Salvar' }}
          <i class="material-icons left">save</i>
        </button>
      </form>

      <table>
        <thead>
          <tr>
            <th>ID</th> 
            <th>Nome</th> 
            <th># Dependentes</th>
            <th>Salário</th> 
            <th>Data de Nascimento</th> 
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="funcionario of funcionarios.slice()" :key="funcionario.id">
            <td>{{ funcionario.id }}</td>
            <td>{{ funcionario.nome }}</td>
            <td>{{ funcionario.numDep }}</td>
            <td>{{'R$ ' + funcionario.salario.toLocaleString('pt-BR', {minimumFractionDigits: 2, maximumFractionDigits: 2}) }}</td>
            <td>{{ new Date(funcionario.nascimento + 'T00:00:00').toLocaleDateString() }}</td>
            <td>
              <button @click="editar(funcionario)" class="waves-effect btn-small blue darken-1">
                <i class="material-icons">create</i>
              </button>

              <button @click="excluir(funcionario)" class="waves-effect btn-small red darken-1">
                <i class="material-icons">delete_sweep</i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
  import Funcionario from './services/funcionarios';

  export default {
    data() {
      return {
        funcionario: {
          id: '',
          nome: '',
          numDep: '',
          salario: '',
          nascimento: ''
        },
        funcionarios: [],
        desabilitado: true,
      }
    },

    mounted() {
      this.listar();
    },

    methods: {
      listar() {
        Funcionario.listar()
          .then(resposta => {
            console.log(resposta);
            this.funcionarios = resposta.data;
          })
          .then( () => { this.limpar(true) } )
      },
      
      limpar(desabilitado = false) {
        this.funcionario = { };
        this.desabilitado = desabilitado;
      },

      incluir() {
        this.limpar();
      },

      editar(funcionario) {
        this.limpar();
        
        // Clona o objeto
        this.funcionario = Object.assign({}, funcionario);
      },

      excluir(funcionario) {
        if (confirm('Deseja mesmo excluir o funcionário?')) {
          Funcionario.excluir(funcionario)
            .then(() => {
              alert('Funcionário excluído com sucesso!');
              this.listar();
            });
        }
      },

      salvar() {
        if (!this.funcionario.id) {
          Funcionario.salvar(this.funcionario).
            then(() => {
              alert('Funcionário salvo com sucesso!');
              this.listar();
            });
        } else {
          Funcionario.alterar(this.funcionario).
            then(() => {
              alert('Funcionário alterado com sucesso!');
              this.listar();
            });
        }
      }
    }
  };
</script>

<style>

</style>
