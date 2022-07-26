# doc-linux-merge

Documentation sur le merge de deux fichiers `slides_a.md` et `slides_b.md` dans un fichier `slides.md`

## diff

[diff](https://man7.org/linux/man-pages/man1/diff.1.html) permet d'inspecter le diff de deux fichiers. En regardant `man diff` on y trouve pas mal d'options.
Afficher les différences de lignes uniquement entres les 2 fichiers

~~~bash
diff -d --color --suppress-common-lines slides_a.md slides_b.md
~~~

## créer un patch

On peut créer un fichier patch `a_to_b.patch` (un fichier de differences entres les deux fichiers) 

~~~bash
diff -u slides_a.md slides_b.md > a_to_b.patch
~~~

Ici le patch contient les instructions pour que le fichier de départ `slides_a.md` soit identique au fichier d'arrivée `slides_b.md`.

Par exemple

~~~bash
--- slides_a.md	2022-07-26 09:14:45.486457000 +0200
+++ slides_b.md	2022-07-26 09:24:37.885238187 +0200
@@ -1,3 +1,3 @@
-foo
-une autre ligne presente que dans ce fichier
+bar
 meme ligne sur les deux fichiers
+une ligne spécifique à ce fichier
~~~

indique comment modifier `slides_a.md` pour qu'il soit comme `slides_b.md`

- retirer la ligne (`-`) qui contient `foo`
- retirer la ligne (`-`) qui contient `une autre ligne presente que dans ce fichier`
- ajouter la ligne (`+`) qui contient `bar`
- ne pas toucher à la ligne (` `) `meme ligne sur les deux fichiers` (commune aux deux)
- ajouter la ligne (`+`) qui contient `ligne spécifique à ce fichier`

## appliquer un patch: modifier un fichier pour qu'il soit identque à l'autre

On peut ensuite appliquer ce patch au fichier `slides_a.md` avec la commande `patch originalfile patchfile` ou `patch -p0 < patchfile`

~~~
patch -p0 <a_to_b.patch --dry-run
~~~

L'option `--dry-run` permet d'afficher le résultat de la commande sans modifier les fichiers. Important pour tester l'esprit tranquille.

Ici, `patch` modifiera le fichier `slides_a.md` (le premier argument de diff) pour qu'il soit identique à `slides_b.md`.

## merge

`patch` dispose également d'une option `--merge`. Permet de merger un fichier patch de manière similaire à la commande `merge`. Si un conflit est trouvé, `patch` ecrit sur la sortie un warning avec des brackets du type
~~~bash
<<<<<<<
lignes du fichier original
|||||||
lignes du fichier du patch
=======
nouvelles lignes du patch
>>>>>>>
~~~

Pour merger le fichier `slides_b.md` dans `slides_a.md` on peut donc faire

~~~
patch -p0 <a_to_b.patch --merge
~~~

Ici on fait un merge *sur place*. Pour faire un merge sans toucher les fichiers d'origine, on peut préciser un fichier de sortie `slides.md` avec l'option `-o`

~~~
patch -p0 <a_to_b.patch --merge -o slides.md
~~~