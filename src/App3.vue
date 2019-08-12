<template>
  <div id="app">
    <!-- обычная html таблица -->
    <table border="1">
      <thead>
        <tr>
          <th v-for="key in Columns" :key="key">{{ key }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="key in Phones" :key="key.number">
          <td>
            <a href="#">{{ key.number }}</a>
          </td>
          <td>{{ key.name }}</td>
          <td>
            <input type="button" value="X" />
          </td>
        </tr>
      </tbody>
    </table>
    <br />
    <form id="search">
      <input ref="number" v-model="number" placeholder="Номер" />
      <br />
      <input v-model="name" placeholder="Имя" />
      <br />
      <br />
      <input
        v-show="id == ''"
        type="button"
        value="Добавить"
        @click="addPhone()"
      />
      <input
        v-show="id != ''"
        type="button"
        value="Обновить"
        @click="updatePhone()"
      />
      <input type="button" value="Очистить" @click="clearForm()" />
    </form>
  </div>
</template>

<script>
import { gql } from "apollo-boost";
// для компактности выделяем фрагмент gql,
// его мы будем часто использовать. Этот фрагмент наша структура таблицы
const fragment = gql`
  fragment Phone on Phone {
    id
    number
    name
  }
`;
// теперь опишем наш основной запрос, константы потом проще использовать в коде
// получаем все записи телефонов
// переопределенный фрагмент - это формат вывода данных, описываем как ...Phone
// аналогично запрос можно описать так
// const ALL_PHONES_QUERY = gql`
//   query Phones {
//     Phones {
//       id
//       number
//       name
//     }
//   }
// `;

const ALL_PHONES_QUERY = gql`
  query Phones {
    Phones {
      ...Phone
    }
  }
  ${fragment}
`;
//Описываем запрос на добавление в формате GraphQL
const ADD_PHONE_MUTATION = gql`
  mutation($input: inputPhone!) {
    addPhoneByInput(input: $input) {
      ...Phone
    }
  }
  ${fragment}
`;
export default {
  name: "App",
  data() {
    return {
      id: "",
      number: "",
      name: "",
      Columns: ["Номер", "Имя", ""] // Это описание столбцов для таблицы
    };
  },
  apollo: {
    Phones: {
      query: ALL_PHONES_QUERY
    }
  },
  methods: {
    clearForm() {
      this.id = "";
      this.number = "";
      this.name = "";
    },
    addPhone() {
      // в методе addPhoneByInput в качестве параметра идет объект
      // если описать каждый параметр, то их можно было бы через запятую передать
      // вида variables: {number,name}
      const input = {
        input: {
          number: this.number,
          name: this.name
        }
      };
      // после изменение данных читаем состояние в базе через запрос refetchQueries
      // Vue обновит данные в интерфейсе сам
      this.$apollo.mutate({
        mutation: ADD_PHONE_MUTATION,
        variables: input,
        refetchQueries: [
          {
            query: ALL_PHONES_QUERY
          }
        ]
      });
      this.clearForm();
    },
    updatePhone() {}
  }
};
</script>
<style></style>;
