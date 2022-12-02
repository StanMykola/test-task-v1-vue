<template lang="">
    <div id="app">
        <div class="check-container">
            <h3>Перевірка правильності номерів будинків</h3>
            <input
                v-model="inputNumber"
                type="text"
                class="form-control"
                placeholder="Введіть номер"
            />
            <span class="check-example">Наприклад: 1, 7Б, 12а, 17/1, 17в/1, 21-5, 27к-1</span>
            <span :class="['check-result', {'incorrect': isCorrect}]">{{ message }}</span>
            <button
                type="button"
                class="btn btn-primary check-btn"
                @click="checkBuildingNumber"
            >
                Перевірити
            </button>
            
        </div>
        <div class="number-container">
            <h5>Список номерів</h5>
            <ul class="number-list">
                <li
                    v-for="number in paginatedNumbers"
                    :key="number.id"
                    :class="[
                        'number-list__item',
                        number.isCorrect ? 'correct' : 'incorrect',
                    ]"
                >
                    {{ number.id }}. {{ number.value }}
                </li>
            </ul>
            <paginate
                :page-count="pageCount"
                :page-range="perPage"
                :margin-pages="2"
                :click-handler="clickHandler"
                :prev-text="'Prev'"
                :next-text="'Next'"
                :container-class="'pagination'"
                :page-class="'page-item'"
            >
            </paginate>
        </div>
    </div>
</template>
<script>
import Paginate from 'vuejs-paginate-next';
import data from './data/buildingNumbers.txt?raw';

export default {
    name: 'App',
    components: {
        Paginate,
    },
    data() {
        return {
            buildingNumbers: [],
            currentPage: 1,
            pageCount: 0,
            perPage: 1000,
            message: '',
            inputNumber: '',
            isCorrect: false
        };
    },
    methods: {
        checkBuildingNumber() {
            if (this.inputNumber) {
                if (this.validateNumber(this.inputNumber)) {
                    this.message = 'Правильний номер';
                    this.isCorrect = false;
                    return;
                }
                this.message = 'Неправильний номер';
                this.isCorrect = true;
                return;
            }
            this.isCorrect = true
            this.message = 'Введіть номер';
        },
        clickHandler(pageNum) {
            this.currentPage = pageNum;
        },
        validateNumber(num) {
            const regexp =
                /(?!0)^[0-9]{1,3}(?![а-щьюяґєії]+[0-9])(?![0-9])[а-щьюяґєії]?[-/]?([0-9]{0,3})$/;
            return !!num.match(regexp);
        },
    },
    mounted() {
        data.split('\n').map((item, id) => {
            this.buildingNumbers.push({
                id: id + 1,
                value: item,
                isCorrect: this.validateNumber(item),
            });
        });
        this.pageCount = Math.ceil(this.buildingNumbers.length / this.perPage);
    },
    computed: {
        indexStart() {
            return (this.currentPage - 1) * this.perPage;
        },
        indexEnd() {
            return this.currentPage * this.perPage;
        },
        paginatedNumbers() {
            return this.buildingNumbers.slice(this.indexStart, this.indexEnd);
        },
    },
};
</script>
<style lang="css">
@import 'https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css';

.number-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 20px;
}

.number-list {
    list-style: none;
    width: 100%;
}

.number-list__item {
    padding: 5px;
    margin: 10px;
    border-radius: 10px;
}

.check-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 10px;
}

.check-example {
    width: 100%;
    text-align: left;
    font-size: 14px;
}

.check-result {
    border-radius: 5px;
    width: 30%;
    font-size: 18px;
    text-align: center;
}

.check-btn {
    width: 100%;
    margin: 10px 0;
}

.correct {
    background-color: rgb(52, 196, 90);
    color: rgb(255, 255, 255);
}

.incorrect {
    background-color: rgb(183, 47, 86);
    color: rgb(255, 255, 255);
}
</style>
