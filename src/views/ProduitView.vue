<template>
    <main>
        <div>
            <h1>Les produits</h1>
            <!-- Un formulaire pour saisir les valeurs de la catégorie à ajouter -->
        </div>
        <div>
            <table>
                <caption>Liste des Produits</caption>
                <div>
                    <p style="display: flex; justify-content: center; position: relative; padding-inline: 125%;">{{data.pages.number + 1 }}/{{ data.pages.totalPages }}</p>
                </div>
                
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Unitées en Stock</th>
                    <th>Unitées Commandées</th>
               
                </tr>
                <!-- Si le tableau des catégories est vide -->
                <tr v-if="data.listeProduits.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des Produits...</td>
                </tr>
                <!-- Si le tableau des catégories n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit.code">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                    
                </tr>
                <tr>
                    <td><button @click="PageLink(data.liens.first.href)" class="fleche">⇇</button></td>
                    <td><button @click="PageLink(data.liens.prev.href)" class="fleche">←</button></td>
                    <td><button @click="PageLink(data.liens.next.href)" class="fleche">→</button></td>
                    <td><button @click="PageLink(data.liens.last.href)" class="fleche">⇉</button></td>
                </tr>
            </table>
        </div>
    </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest } from "@/api";


let data = reactive({
    // La liste des catégories affichée sous forme de table
    listeProduits: [],
    liens: [],
    pages: []
});

function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

function chargeProduits() {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("/api/produits?page=&size=5")
        .then((json) => {
            
            data.listeProduits = json._embedded.produits;
            data.liens = json._links;
            data.pages = json.page;
        })
        .catch(showError);
}

function PageLink(url){
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    if(url !== null){
        doAjaxRequest(url)
        .then((json) => {

            data.listeProduits = json._embedded.produits;
            data.liens = json._links;
            data.pages = json.page;
        })
        .catch(showError);
    }
    else {
        console.log("Vous êtes arrivé en bout de liste !");
    }
    

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
onMounted(chargeProduits);

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
