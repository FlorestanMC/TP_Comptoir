<template>
    <main>
        <div>
            <h1>Les Produits/Catégorie</h1>
            <!-- Un formulaire pour saisir les valeurs de la catégorie à ajouter -->
        </div>
        <div>
            <table>
                
                <label for="categorie">Choisissez une catégorie :</label>
                <select name="categorie" id="categorie" v-model="catchoisie">
                    <option v-for="categorie in data.listeCategories" :key="categorie.code" v-bind:value="categorie.code">{{ categorie.libelle }}</option>
                </select>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Unitées en Stock</th>
                    <th>Unitées Commandées</th>
               
                </tr>
                <!-- Si le tableau des catégories est vide -->
                <tr v-if="data.listeProduits.length === 0">
                    <td colspan="4">↑↑ Séléctionnez une catégorie de produits ↑↑</td>
                </tr>
                <!-- Si le tableau des catégories n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit.code">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                    
                </tr>
               
            </table>
        </div>
    </main>
</template>

<script setup>
import { reactive, onMounted, ref, watch } from "vue";
import { doAjaxRequest } from "@/api";



let data = reactive({
    // La liste des catégories affichée sous forme de table
    listeCategories: [],
    listeProduits: []
});

let catchoisie = ref("");

function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

function chargeCategories() {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("/api/categories?sort=code,desc")
        .then((json) => {
            
            data.listeCategories = json._embedded.categories;
        })
        .catch(showError);
}

function chargeProduits(categorie) {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("/api/categories/"+categorie+"/produits")
        .then((json) => {
            
            data.listeProduits = json._embedded.produits;
            data.liens = json._links;
            data.pages = json.page;
        })
        .catch(showError);
}




/**
 * Supprime une entité
 * @param entityRef l'URI de l'entité à supprimer
 */
function deleteEntity(entityRef) {
    doAjaxRequest(entityRef, { method: "DELETE", headers: { "Accept": "application/json" }})
        .then(chargeProduits)
        .catch(showError);
}

// A l'affichage du composant, on affiche la liste
onMounted(chargeCategories);

watch(catchoisie, ()=>{
    chargeProduits(catchoisie.value)
});

</script>


<style scoped>
td,
th {
    border: 1px solid #ddd;
    padding: 8px;
    justify-content: center;
    
}

th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #232623;
    color: rgb(255, 255, 255);
}



.fleche {
    display: flex;
    justify-content: center;
    padding: 5px;
    width: 25px;

    border-radius: 10%;
    background-color: white;
    text-align: center;
}
</style>
