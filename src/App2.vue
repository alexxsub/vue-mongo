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
// теперь опишем наш запрос основной
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
export default {
  name: "app",
  data() {
    return {
      Columns: ["Номер", "Имя", ""] // Это описание столбцов для таблицы
    };
  },
  apollo: {
    Phones: {
      query: ALL_PHONES_QUERY
    }
  }
};
</script>
<style></style>;
