# hypergraphs

Uses:

    from bhg import Graph
    import time
    
    g = Graph()
    
    st = time.time()
    
    node_types = ['wiki', 'nic', 'mac']
    data = [
        {'wiki': '101', 'nic': '101--1/0/1', 't1':0, 't2': 1, 'mac':'00-11-22', 'source': 'mac'},
        {'wiki': '102', 'nic': '102--1/0/1', 't1':0, 't2': 2, 'mac':'01-11-22', 'source': 'mac'},
        {'wiki': '103', 'nic': '103--1/0/1', 't1':0, 't2': 3, 'mac':'02-11-22', 'source': 'mac'},
        {'wiki': '101', 'nic': '101--1/0/1', 't1':100, 't2': 1, 'mac':'00-11-22', 'source': 'mac'},
    ]
    
    g.add_node_types(*node_types)

    for ed in data:
        n_ids, p = g.separate_keys(ed)
        et = p.pop('source')
        n = list(g.nodes(**n_ids))
        _e = g.edges(*n, type = et)
        if not _e:
            _e = {Edge(g, type = et, nodes = n, **p)}
        else:
            for __e in _e:
                __e.update(**p)
    
    print(time.time() - st ,'s')
    
    for _e in g._edges:
        for __e in g._edges[_e]:
            print('t3' in __e)
            print(__e.dump_dict())
    
 Uses with db:
 
    from bhg.db import GraphDB, toDictIterator

    indexes = ['wiki', 'nic', 'mac']
    data = [
        {'wiki': '101', 'nic': '101--1/0/1', 't1':0, 'mac':'00-11-22', 'source': 'mac'},
        {'wiki': '102', 'nic': '102--1/0/2', 't1':0, 'mac':'00-11-22', 'source': 'mac'},
        {'wiki': '103', 'nic': '103--1/0/1', 't1':0, 'mac':'02-11-22', 'source': 'mac'},
        {'wiki': '101', 'nic': '101--1/0/1', 't1':100, 'mac':'00-11-22', 'source': 'mac'},
        {'wiki': '101', 'nic': '101--1/0/1', 't1':120, 'mac':'00-11-22', 'source': 'mac'},
    ]

    db = GraphDB(indexes)
    
    for d in data:
        table = d.pop('source', 'unk')
        db.update_or_insert(table, d)

    query = db.nodes.filter(nic__endswith = '1/0/1', mac = '00-11-22')
    
    for i in toDictIterator(query.edges):
        print(i)
