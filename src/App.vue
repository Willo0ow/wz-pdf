<template>
  <div>
    <input type="text" v-model="nrWZ" placeholder="Nr dokumentu (wpisywać bez WZ)">
    <input type="text" v-model="odbiorca.nazwa" placeholder="Nazwa odbiorcy">
    <input type="text" v-model="odbiorca.ulica" placeholder="Odbiorca - ulica nr budynku">
    <input type="text" v-model="odbiorca.miasto" placeholder="Odbiorca - miasto, kod pocztowy">
    <input type="text" v-model="odbiorca.nip" placeholder="Odbiorca - NIP">
    <input type="text" v-model="odbiorca.tel" placeholder="Odbiorca - nr telefonu">
<div v-for="(el,idx) of items" v-if="items.length>0">
  <p>Pozycja {{idx+1}}</p>
    <input type="text" v-model="items[idx].nazwa" placeholder="Nazwa towaru">
    <input type="number" v-model="items[idx].cena" placeholder="cena">
    <input type="text" v-model="items[idx].jm" placeholder="jednostka miary">
    <input type="number" v-model="items[idx].ilosc" placeholder="ilość">

</div>
    <button @click="items.push({cena:0, ilosc:0})">Dodaj pozycję</button>
    <button @click="some">table</button>
    <button @click="pdfTable()">create table</button>
  </div>
</template>

<script>
import pdfMake from "pdfmake/build/pdfmake";
import pdfFonts from "pdfmake/build/vfs_fonts";
pdfMake.vfs = pdfFonts.pdfMake.vfs;
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';
import 'jspdf-autotable'
export default {
  name: 'app',
  data () {
    return {
      items:[{cena:0, ilosc:0}],
      odbiorca:{},
      doc:null,
      nrWZ:null,
      rows:[],
      row:null,
      activeRow:false,
      sth:		{content:[
        //top line columns [0]
        {
        alignment: 'justify',
        columns: [
          {text: 'Logo Firmy', style: 'header'},
          {text: ''},
          {text: 'Data wystawienia'}]
        },
        //second line  [1]
        'text',
        //third line columns  [2]
        {
          alignment: 'justify',
          columns: [
            {text: 'Sprzedawca'},
            {text: 'Odbiorca'}]
        },
        //table [3]
        {
          color: '#444',
          table: {
            widths: [20, 230,50,50,50,50 ],
            //headers
            body: [
              [{text: 'Lp.', style: 'tableHeader', alignment: 'center'}, 
              {text: 'Nazwa produktu/materiału', style: 'tableHeader', alignment: 'center'},
              {text: 'cena', style: 'tableHeader', alignment: 'center'},
              {text: 'j.m.', style: 'tableHeader', alignment: 'center'},
              {text: 'ilość', style: 'tableHeader', alignment: 'center'},
              {text: 'wartość', style: 'tableHeader', alignment: 'center'},
              ]
              //function appends items and sum
              
            ]
          },
          layout: {
				      fillColor: function (rowIndex, node, columnIndex) {
                return (rowIndex == 0) ? '#CCCCCC' : null;
            }
          }
        },
        //last line columns
        {
        alignment: 'justify',
        columns: [
          {text: '.......................................................', alignment: 'center'},
          {text: '.......................................................', alignment: 'center'}]
        },
        {
        alignment: 'justify',
        columns: [
          {text: 'Towar sprawdził i odebrał', alignment: 'center'},
          {text: 'Towar wydał', alignment: 'center'}]
        },
      ]},
    }
  },
  computed:{
    suma(){
      let sum = this.items.reduce((sum, val)=>{
        sum += (parseFloat(val.cena) * parseFloat(val.ilosc))
        return sum
      },0)
      return sum.toFixed(2)
    }
  },
  methods:{
    some(){pdfMake.createPdf(this.sth).open()},
  
  pdfTable(){
    this.tableItems()
    this.tableSum()
    this.odbiorcaTable()
    this.dataTitleTable()
  },
  tableSum(){
     this.sth.content[3].table.body.push([{colSpan:5, text: 'Razem', style: 'tableHeader', alignment: 'right'}, {},{},{},{},{text:this.suma}])
  },
  tableItems(){
    let self = this
    this.items.forEach((el,idx)=>{
      let wartosc = parseFloat(el.cena) * parseFloat(el.ilosc)
      let cena = parseFloat(el.cena)
      this.sth.content[3].table.body.push(
        [{text: (idx+1 + '.'), style: 'tableHeader', alignment: 'center'}, 
          {text: el.nazwa, style: 'tableHeader', alignment: 'center'},
          {text: cena.toFixed(2), style: 'tableHeader', alignment: 'center'},
          {text: el.jm, style: 'tableHeader', alignment: 'center'},
          {text: el.ilosc, style: 'tableHeader', alignment: 'center'},
          {text: wartosc.toFixed(2), style: 'tableHeader', alignment: 'center'},
          ]
      )
    })
  },
  odbiorcaTable(){
    let rec = this.odbiorca
    this.sth.content[2].columns[1].text = ['Odbiorca:\n', rec.nazwa + '\n', rec.ulica + '\n', rec.miasto + '\n', rec.nip + '\n', rec.tel + '\n']
  },
  dataTitleTable(){
    let date = new Date().toISOString().slice(0,10)
    this.sth.content[0].columns[2].text = 'Data wystawienia: ' + date
    this.sth.content[1] = 'WZ - wydanie zewnętrzne - ' + this.nrWZ
  }
}}
</script>

<style>

table {
  font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
  border: red solid 2px;
  font-size: 0.7em;
}

 td, th {
  border: 1px solid rgb(190, 101, 101);
  padding: 3px;
}


#customers th {

  text-align: center;
  background-color: rgb(52, 51, 61);
  
  color: white;
}
.parent{
  padding: 0;
}
.small{
  height: 100%;
}
tfoot td{
  padding: 20px 3px;
}
input{
  width: 90%;
  border: none;
  background: rgba(128, 128, 128, 0.144);
  margin: 3px 5px;
  padding: 3px;
}
input:focus{
  outline: none;
}
.activeRow{
  background-color: rgb(148, 148, 134);
}
</style>
