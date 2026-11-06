<script setup>
useHead({title:"ISONO — Vintage Audio",htmlAttrs:{lang:"fr"},meta:[{name:"description",content:"La communauté des passionnés de high-end vintage."},{name:"theme-color",content:"#0b0907"}],link:[{rel:"icon",href:"/assets/favicon.svg",type:"image/svg+xml"}]});
onMounted(() => {
(() => {
  'use strict';
  const dialog = document.querySelector('.modal');
  const body = dialog.querySelector('.modal-body');
  const sections = [
    {id:'boutique', title:'Boutique', text:'Une sélection de matériels vintage, révisés et garantis.', detail:'Amplificateurs, platines, magnétophones et accessoires : un espace pour retrouver le plaisir des beaux appareils.'},
    {id:'marketplace', title:'Marketplace', text:'Acheter et vendre entre passionnés.', detail:'Un futur espace d’annonces pour faire circuler les appareils, rencontrer d’autres collectionneurs et trouver la pièce que vous cherchez.'},
    {id:'echange', title:'Échange', text:'Une nouvelle histoire pour vos appareils.', detail:'Proposez une pièce de votre collection et découvrez les envies d’échange des autres passionnés.'},
    {id:'forum', title:'Forum', text:'La passion se partage.', detail:'Questions, découvertes, retours d’expérience et projets de restauration : le futur lieu de discussion de la communauté ISONO.'},
    {id:'collection', title:'Collection', text:'Chaque collection raconte une histoire.', detail:'Un espace pour présenter vos appareils, partager leurs histoires et découvrir les collections des autres membres.'},
    {id:'service', title:'Service', text:'Préserver le son et le savoir-faire.', detail:'Un futur espace pour trouver des spécialistes de l’entretien, de la réparation et de la restauration de matériel vintage.'}
  ];
  const articles = [
    {id:'technics', title:'Technics : l’âge d’or de la Hi-Fi japonaise', text:'Histoire, modèles mythiques et conseils de restauration.', detail:'Ce premier rendez-vous éditorial sera consacré à Technics et aux appareils qui ont marqué les collections des passionnés.'},
    {id:'vinyle', title:'Bien entretenir sa platine vinyle', text:'Nos conseils pratiques pour préserver votre matériel.', detail:'Un prochain guide réunira les gestes d’entretien et les points à vérifier pour prendre soin de votre platine.'}
  ];
  let previousFocus = null;
  const element = (tag, text, className) => {
    const node = document.createElement(tag);
    if (text) node.textContent = text;
    if (className) node.className = className;
    return node;
  };
  function show(title, kicker, paragraphs, action) {
    previousFocus = dialog.open ? previousFocus : document.activeElement;
    body.replaceChildren();
    body.append(element('p', kicker, 'modal-kicker'));
    const heading = element('h2', title);
    heading.id = 'modal-title';
    body.append(heading);
    paragraphs.forEach(text => body.append(element('p', text)));
    if (action) {
      const actions = element('div', '', 'modal-actions');
      const button = element('button', action.text, 'button gold');
      button.addEventListener('click', action.run);
      actions.append(button);
      body.append(actions);
    }
    if (!dialog.open) dialog.showModal();
  }
  function openSection(id) {
    const section = sections.find(item => item.id === id);
    if (section) {
      show(section.title, 'Les univers ISONO', [section.text, section.detail, 'Cet espace ouvrira prochainement. Pour le moment, découvrez l’accueil et les six univers du projet.']);
      return;
    }
    const article = articles.find(item => item.id === id);
    if (article) {
      show(article.title, 'À la une · Aperçu éditorial', [article.text, article.detail, 'L’article complet sera disponible lors de l’ouverture de cette rubrique.']);
      return;
    }
    const utility = {
      compte: ['Mon compte', 'Votre espace personnel', 'Vous pourrez retrouver vos appareils, vos annonces et vos échanges dans un même espace. La création de compte sera proposée à l’ouverture de la communauté.'],
      favoris: ['Vos favoris', 'Les belles découvertes se gardent', 'Votre sélection est vide. Vous pourrez enregistrer ici les appareils et les annonces qui vous plaisent lorsque les rubriques seront ouvertes.'],
      panier: ['Votre panier', 'La boutique ISONO', 'Votre panier est vide. La boutique ouvrira dans une prochaine étape du projet ; aucun achat n’est proposé dans cet aperçu.'],
      langue: ['Bonjour, bienvenue sur ISONO', 'Langue du site : français', 'Cette première version est présentée en français. D’autres langues pourront être ajoutées par la suite.'],
      communaute: ['La communauté ISONO', 'La passion du son vintage', 'Un lieu pour les collectionneurs, les curieux et tous ceux qui aiment les beaux appareils. Le forum et les comptes membres ouvriront progressivement.']
    };
    const info = utility[id];
    if (info) show(info[0], info[1], [info[2]], id === 'communaute' ? {text:'Découvrir les univers', run:() => {dialog.close(); document.querySelector('#univers').scrollIntoView({behavior: window.matchMedia('(prefers-reduced-motion: reduce)').matches ? 'instant' : 'smooth'});}} : null);
  }
  document.addEventListener('click', event => {
    const trigger = event.target.closest('[data-open]');
    if (trigger) {event.preventDefault(); openSection(trigger.dataset.open);}
  });
  dialog.querySelector('.modal-close').addEventListener('click', () => dialog.close());
  dialog.addEventListener('click', event => {
    if (event.target !== dialog) return;
    const rect = dialog.getBoundingClientRect();
    if (event.clientX < rect.left || event.clientX > rect.right || event.clientY < rect.top || event.clientY > rect.bottom) dialog.close();
  });
  dialog.addEventListener('close', () => previousFocus?.focus({preventScroll:true}));
  const normalize = text => text.normalize('NFD').replace(/[\u0300-\u036f]/g, '').toLocaleLowerCase('fr');
  document.querySelector('.search-form').addEventListener('submit', event => {
    event.preventDefault();
    const query = document.querySelector('[name=q]').value.trim();
    const matches = [...sections, ...articles].filter(item => normalize(item.title+' '+item.text+' '+item.detail).includes(normalize(query)));
    show(query ? 'Résultats pour « '+query+' »' : 'Explorer ISONO', 'Recherche', []);
    if (!matches.length) {body.append(element('p', 'Aucun résultat pour cette recherche. Essayez « vinyle », « collection » ou « Technics ».')); return;}
    const list = element('div', '', 'search-results');
    matches.forEach(item => {
      const button = element('button', '', 'search-result');
      button.append(element('strong', item.title), element('span', item.text));
      button.addEventListener('click', () => openSection(item.id));
      list.append(button);
    });
    body.append(list);
  });
})();

});
</script>
<template>
<div>

<a class="skip-link" href="#main">Aller au contenu</a>
<header class="site-header"><div class="header-inner">
<a class="brand" href="#" aria-label="ISONO Vintage Audio — Accueil"><svg class="brand-mark" viewBox="0 0 66 66" aria-hidden="true" fill="none" stroke="currentColor" stroke-width="1.6"><path d="M4 62 A58 58 0 0 1 62 4"/><path d="M8 62 A54 54 0 0 1 62 8"/><path d="M12 62 A50 50 0 0 1 62 12"/><path d="M16 62 A46 46 0 0 1 62 16"/><path d="M20 62 A42 42 0 0 1 62 20"/><path d="M24 62 A38 38 0 0 1 62 24"/><path d="M28 62 A34 34 0 0 1 62 28"/><path d="M32 62 A30 30 0 0 1 62 32"/></svg><span class="brand-type"><strong><img src="/assets/isono-wordmark.svg" alt="ISONO"></strong><span><img src="/assets/isono-subtitle.svg" alt="Vintage Audio"></span></span></a>
<p class="tagline" aria-label="La communauté des passionnés de high-end vintage"><span class="tagline-art" aria-hidden="true"></span></p>
<form class="search-form" role="search"><button type="submit" aria-label="Rechercher"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><circle cx="10.5" cy="10.5" r="6.5"/><path d="m16 16 5 5"/></svg></button><input type="search" name="q" aria-label="Rechercher sur ISONO" placeholder="Rechercher…" title="Rechercher un appareil, une marque…" autocomplete="off"></form>
<div class="header-actions"><button class="icon-button" data-open="compte" aria-label="Mon compte"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="7" r="4"/><path d="M3 22v-3a7 7 0 0 1 7-7h4a7 7 0 0 1 7 7v3H3Z"/></svg><span>Mon compte</span></button><button class="icon-button" data-open="favoris" aria-label="Favoris"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M20.8 4.6a5.5 5.5 0 0 0-7.8 0L12 5.7l-1.1-1.1a5.5 5.5 0 0 0-7.8 7.8L12 22l8.8-9.6a5.5 5.5 0 0 0 0-7.8Z"/></svg><span>Favoris</span></button><button class="icon-button cart-button" data-open="panier" aria-label="Mon panier"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M2 3h3l3 13h11l3-10H6M9 21h.01M18 21h.01"/><circle cx="9" cy="20" r="1"/><circle cx="18" cy="20" r="1"/></svg></button><button class="icon-button language-button" data-open="langue" aria-label="Langue : français"><span>FR</span><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="m6 9 6 6 6-6"/></svg></button></div>
</div></header>
<nav class="primary-nav" aria-label="Navigation principale"><div class="nav-inner"><a class="nav-link active" href="#" aria-current="page"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="m3 10 9-7 9 7M5 9v12h5v-7h4v7h5V9"/></svg>Accueil</a><a class="nav-link" href="#boutique" data-open="boutique"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><rect x="4" y="6" width="16" height="15" rx="2"/><path d="M9 8V5a3 3 0 0 1 6 0v3"/></svg>Boutique</a><a class="nav-link" href="#marketplace" data-open="marketplace"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M2 3h3l3 13h11l3-10H6M9 21h.01M18 21h.01"/><circle cx="9" cy="20" r="1"/><circle cx="18" cy="20" r="1"/></svg>Marketplace</a><a class="nav-link" href="#echange" data-open="echange"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M20 8A8 8 0 0 0 6 5L3 8m0-5v5h5M4 16a8 8 0 0 0 14 3l3-3m0 5v-5h-5"/></svg>Échange</a><a class="nav-link" href="#forum" data-open="forum"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M21 11a8 8 0 0 1-8 8H8l-5 3 1-6a8 8 0 0 1-1-5 8 8 0 0 1 8-8h2a8 8 0 0 1 8 8Z"/></svg>Forum</a><a class="nav-link" href="#collection" data-open="collection"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="m12 3 10 5-10 5L2 8l10-5Zm-10 9 10 5 10-5M2 16l10 5 10-5"/></svg>Collection</a><a class="nav-link" href="#service" data-open="service"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M14 6a6 6 0 0 0-7 8L2 19a2 2 0 0 0 3 3l5-5a6 6 0 0 0 8-7l-4 4-4-4 4-4Z"/></svg>Service</a></div></nav>
<main id="main">
<section class="hero" aria-labelledby="hero-title"><div class="hero-art"><svg class="photo" viewBox="510 143 706 460" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Platine cassette Nakamichi Dragon sur un meuble en bois"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></div><div class="hero-shade"></div><div class="hero-inner"><div class="hero-copy"><h1 id="hero-title">Acheter, vendre, échanger,<br>restaurer et partager<br>la passion du son vintage</h1><p class="hero-description">Le site de référence pour les amateurs<br>de matériels audio classiques<br>et de haute-fidélité.</p><div class="hero-actions"><a class="button gold" href="#univers">Découvrir ISONO <svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></a><button class="button outline" data-open="communaute">Rejoindre la communauté</button></div></div></div></section>
<section class="section-grid" id="univers" aria-label="Les six univers ISONO"><button class="section-card" data-open="boutique" id="boutique"><span class="card-photo"><svg class="photo" viewBox="16 606 187 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Amplificateur vintage à façade argentée"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><rect x="4" y="6" width="16" height="15" rx="2"/><path d="M9 8V5a3 3 0 0 1 6 0v3"/></svg><span>Boutique</span></span><span class="card-description">Notre sélection de matériels vintage, révisés et garantis.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button><button class="section-card" data-open="marketplace" id="marketplace"><span class="card-photo"><svg class="photo" viewBox="218 606 185 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Platine vinyle et disque noir"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M2 3h3l3 13h11l3-10H6M9 21h.01M18 21h.01"/><circle cx="9" cy="20" r="1"/><circle cx="18" cy="20" r="1"/></svg><span>Marketplace</span></span><span class="card-description">Achetez et vendez entre passionnés. Trouvez votre prochaine pièce de collection.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button><button class="section-card" data-open="echange" id="echange"><span class="card-photo"><svg class="photo" viewBox="417 606 186 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Collection de cassettes audio"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M20 8A8 8 0 0 0 6 5L3 8m0-5v5h5M4 16a8 8 0 0 0 14 3l3-3m0 5v-5h-5"/></svg><span>Échange</span></span><span class="card-description">Échangez vos appareils et accessoires en toute simplicité.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button><button class="section-card" data-open="forum" id="forum"><span class="card-photo"><svg class="photo" viewBox="617 606 185 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Vu-mètre analogique éclairé"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M21 11a8 8 0 0 1-8 8H8l-5 3 1-6a8 8 0 0 1-1-5 8 8 0 0 1 8-8h2a8 8 0 0 1 8 8Z"/></svg><span>Forum</span></span><span class="card-description">Discutez, posez vos questions et partagez vos expériences.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button><button class="section-card" data-open="collection" id="collection"><span class="card-photo"><svg class="photo" viewBox="816 606 185 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Étagères de matériel haute-fidélité"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="m12 3 10 5-10 5L2 8l10-5Zm-10 9 10 5 10-5M2 16l10 5 10-5"/></svg><span>Collection</span></span><span class="card-description">Présentez votre collection, découvrez celles des autres membres.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button><button class="section-card" data-open="service" id="service"><span class="card-photo"><svg class="photo" viewBox="1015 606 185 122" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Réparation d’un circuit électronique"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></span><span class="card-body"><span class="card-heading"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M14 6a6 6 0 0 0-7 8L2 19a2 2 0 0 0 3 3l5-5a6 6 0 0 0 8-7l-4 4-4-4 4-4Z"/></svg><span>Service</span></span><span class="card-description">Trouvez un spécialiste pour la réparation, l’entretien et la restauration de votre matériel vintage.</span><span class="card-arrow"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></span></span></button></section>
<section class="editorial" aria-labelledby="editorial-title"><div class="section-heading"><h2 id="editorial-title">À la une sur ISONO</h2><hr></div><div class="article-grid"><article class="article-card"><div class="article-photo"><svg class="photo" viewBox="17 971 373 217" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Vu-mètres dorés d’un appareil Technics"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></div><div class="article-copy"><span class="tag">Actualité</span><h3>Technics : l’âge d’or de la Hi-Fi japonaise</h3><p>Histoire, modèles mythiques et conseils de restauration.</p><button class="text-link" data-open="technics">Lire l’article <svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></button></div></article><article class="article-card"><div class="article-photo"><svg class="photo" viewBox="660 971 312 217" preserveAspectRatio="xMidYMid slice" role="img" aria-label="Cellule et bras de lecture sur un disque vinyle"><image href="/assets/reference.jpeg" width="1216" height="1294"/></svg></div><div class="article-copy"><span class="tag">Guide</span><h3>Bien entretenir sa platine vinyle</h3><p>Nos conseils pratiques pour préserver votre matériel.</p><button class="text-link" data-open="vinyle">Voir le guide <svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12h18m-6-6 6 6-6 6"/></svg></button></div></article></div></section>
<div class="brand-strip" aria-label="Quelques marques emblématiques de la Hi-Fi"><img class="brand-logo brand-nakamichi" src="/assets/brands/nakamichi.svg" alt="Nakamichi" loading="lazy" decoding="async"><img class="brand-logo brand-technics" src="/assets/brands/technics.svg" alt="Technics" loading="lazy" decoding="async"><img class="brand-logo brand-sony" src="/assets/brands/sony.svg" alt="SONY" loading="lazy" decoding="async"><img class="brand-logo brand-pioneer" src="/assets/brands/pioneer.svg" alt="PIONEER" loading="lazy" decoding="async"><img class="brand-logo brand-akai" src="/assets/brands/akai.svg" alt="AKAI" loading="lazy" decoding="async"><img class="brand-logo brand-teac" src="/assets/brands/teac.svg" alt="TEAC" loading="lazy" decoding="async"><img class="brand-logo brand-denon" src="/assets/brands/denon.svg" alt="DENON" loading="lazy" decoding="async"><small>et bien d’autres…</small></div>
</main>
<footer class="site-footer"><span>ISONO · Vintage Audio</span><span>Les services seront ouverts progressivement.</span><a href="#">Retour en haut ↑</a></footer>
<dialog class="modal" aria-labelledby="modal-title"><button class="modal-close icon-button" aria-label="Fermer"><svg class="icon" aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="m5 5 14 14M5 19 19 5"/></svg></button><div class="modal-body"></div></dialog>

</div>
</template>
<style src="./assets/main.css"></style>
