# 0-1 BFS

Задача: вам дан взвешенный граф $G$, веса рёбер которого принимают
значения $0$ ($0$-рёбра) и $1$ ($1$-рёбра) и выделена вершина $v$
в нём. Вас просят найти в этом графе кратчайшие расстояния от $v$ до
всех остальных вершин.

Идея: если от вершины $v_1$ до вершины $v_2$ можно дойти по пути,
состоящему из рёбер веса $0$, то кратчайшие расстояния до этих
вершин совпадают (Доказательство остаётся в качестве упражнения
читателю =) ).

Заметим тогда, что если в нашем графе оставить только $0$-рёбра, то он
распадётся на компоненты связности (возможно, некоторые будут иметь
размер $1$), в каждой из которых ответ одинаковый. Если теперь
вернуть $1$-рёбра, и сказать, что эти рёбра соединяют не вершины,
а компоненты связности, то мы сведём задачу к обычному
$\\textbf{BFS}$-у.

А чтобы решить исходную задачу, надо при посещении первой вершины из
компоненты обойти всю компоненту, проставив во всех вершинах такой
же ответ, как и у первой вершины. Как же можно обойти всю компоненту?
Запустить $\\textbf{BFS}$ на $0$-рёбрах\!.

А теперь заметим, что на самом деле нам не нужно запускать
$\\textbf{BFS}$ внутри $\\textbf{BFS}$, достаточно при посещении вершины
добавлять всех её непосещённых соседей по $0$-рёбрам в голову очереди,
чтобы обработать их раньше, чем следующие в очереди.

А какая структура данных позволяет нам добавлять вершины не только в
хвост, но и в голову? Это же дэк\!

В итоге, наш алгоритм выглядит примерно так:

``` C++
vector<int> zero-one-bfs(int start_vertex) {
    // длина любого кратчайшего пути не превосходит n - 1,
    // поэтому n - достаточное значение для "бесконечности";
    // после работы алгоритма dist[v] = n <=> v недостижима из s
    vector<int> dist(n, n);
    dist[s] = 0;
    deque<int> pending;
    pending.push_back(s);

    while (!q.empty()) {
        int v = pending.front();
        pending.pop_front();
        for (auto&& [to, w] /* конец ребра и его вес */ : adj[v]) {
            if (dist[to] > dist[v] + w) {
                dist[to] = dist[v] + w;
                if (w == 0) {
                    pending.push_front(to);
                } else {
                    pending.push_back(to);
                }
            }
        }
    }

    return dist;
}
```