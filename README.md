# Mesure et affichage de la vitesse grâce à un accéléromètre

### Description du projet

Le projet consiste à élaborer un veste de motard affichant la vitesse maximale atteinte par la moto pendant la session. Un bouton permet de ré-initialiser les conditions initiales de mesure, et la vitesse maximale en mémoire. Le système doit être autonome et fonctionner sous batterie. 

### Fondement théorique de la mesure

Un accéléromètre permet d'acquérir les données d'accélération sur chaque axe ($x$, $y$ et $z$), avec comme unité $g$ l'accélération de la gravité , convertible en $m.s^{-1}$ grâce à la relation de proportionnalité :  

$$
1g = 9,8066 ms^{-1}
$$

La vitesse est l'intégrale (notée $\int$) de l'accélération par rapport au temps, soit la somme infinitésimale de l'accélération au cours du temps.
Soit $t_0$ le moment de déclenchement de la mesure. La vitesse $V$ au moment de mesure ultérieur $t_m > t_0$ s'écrit : 

$$
V(t_m)= \int_{t_0}^{t_m} a(t)dt  + V(t_0)
$$

On voit que $V(t_0)$, la vitesse au temps $t_0$ est nécessaire pour obtenir la mesure à un moment ultérieur $t_m$ . Par conséquent il est nécessaire de **connaître la vitesse au moment du démarrage de la mesure.**  
De plus, l'accélération n'est pas connue à tout instant, mais seulement aux points d'acquisition du capteur. On ne connaît pas $a(t)$ pour $t$ un temps arbitraire. Il faut donc *discrétiser* l'intégrale, c'est à dire l'approximer par la somme des mesures multiplié par l'intervalle de temps qui les sépare. **On ne peux obtenir qu'une approximation de la vitesse** (dont la précision dépend de la fréquence d'échantillonnage).
On approxime donc l'intégrale tel que suit : 

$$
V(t_m) \simeq 1/f\sum_{i=0}^{\llcorner (t_m-t_0) * f \lrcorner } a_i + V(t_0)
$$

Où $a_i$ est la suite des valeurs mesurées par le capteur à intervalle régulier de $1/f$  seconde. $\llcorner (t_m-t_0) * f \lrcorner$ est le nombre de ces mesures entre $t_0$ et $t_m$.
La formule se lit : La vitesse au temps $t_m$ est environ égale à $1/f$ multiplié par la somme des accélérations mesurées entre $t_0$et $t_m$, plus la vitesse au temps $t_0$.

Définissons $V_n$ tel que 

$$
V_n = 1/f\sum_{i=0}^{\llcorner (t_m-t_0) * f \lrcorner } a_i + V(t_0)
$$

. Autrement dit, $a_n$ désigne la $n^{ième}$ valeur de l'accélération mesurée depuis $t_0$, et $V_n$ est donc la vitesse correspondante. On remarque que : $V_{n+1} =V_n+1/f*a_{n+1}$
Donc on trouve la $n^{ème}$ vitesse en additionnant à la précédente la $n^{ième}$ accélération multiplié par l'intervalle de temps entre chaque mesure.  **La vitesse est la somme des accélérations mesurées, pondérée par l'intervalle de temps qui les sépares, plus la vitesse initiale.** 

(On pourrait également choisir d'approximer l'intégrale avec des trapèzes )

Une vitesse se mesure sur 3 dimensions, il faut donc faire la norme.

## Implémentation

### Montage

 Nous utilisons un microcontrôleur  *ESP32* pour centraliser et traiter les données, un capteur *SEN-MPU6050* pour acquérir l'accélération, et un *switch* pour commencer et arrêter l'acquisition. Le système est alimenté par une batterie XXXXX montée sur un XXX pour permettre sa recharge. 
*schéma du montage*
*photos du montage*

### Software

Comme nous l'avons vu, on trouve la valeur suivante de la vitesse en lui ajoutant la dernière accélération mesurée divisée par la fréquence d'échantillonnage. L'implémentation est donc assez simple : elle consiste principalement en une boucle dont chaque itération incrémente la variable $vitesse$ de la dernière valeur mesurée divisée par *f* la fréquence de la mesure.  Il faut toutefois s'assurer 

