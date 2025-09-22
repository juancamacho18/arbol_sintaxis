# arbol_sintaxis
import networkx as nx
import matplotlib.pyplot as plt

class Parser:
    def __init__(self, tokens):
        self.tokens = tokens
        self.pos = 0
        self.G = nx.DiGraph()
        self.node_id = 0

    def new_node(self, label):
        node_name = f"{label}_{self.node_id}"
        self.node_id += 1
        self.G.add_node(node_name, label=label)
        return node_name

    def current_token(self):
        if self.pos < len(self.tokens):
            return self.tokens[self.pos]
        return None

    def eat(self, expected=None):
        tok = self.current_token()
        if tok is None:
            return False
        if expected is not None and tok != expected:
            return False
        self.pos += 1
        return True

    def parse(self):
        root = self.parse_E()
        if root and self.pos == len(self.tokens):
            return root, self.G
        else:
            return None, None

    def parse_E(self):
        # E -> T {( + | - ) T}*
        node_E = self.new_node("E")
        left = self.parse_T()
        if left is None:
            return None
        self.G.add_edge(node_E, left)

        while True:
            tok = self.current_token()
            if tok in ('+', '-'):
                op_node = self.new_node("opsuma")
                sym_node = self.new_node(tok)
                self.eat(tok)
                right = self.parse_T()
                if right is None:
                    return None
                self.G.add_edge(node_E, op_node)
                self.G.add_edge(op_node, sym_node)
                self.G.add_edge(node_E, right)
            else:
                break
        return node_E

    def parse_T(self):
        # T -> F {( * | / ) F}*
        node_T = self.new_node("T")
        left = self.parse_F()
        if left is None:
            return None
        self.G.add_edge(node_T, left)

        while True:
            tok = self.current_token()
            if tok in ('*', '/'):
                op_node = self.new_node("opmul")
                sym_node = self.new_node(tok)
                self.eat(tok)
                right = self.parse_F()
                if right is None:
                    return None
                self.G.add_edge(node_T, op_node)
                self.G.add_edge(op_node, sym_node)
                self.G.add_edge(node_T, right)
            else:
                break
        return node_T

    def parse_F(self):
        # F -> num | ( E )
        tok = self.current_token()
        if tok is None:
            return None

        node_F = self.new_node("F")
        if tok.isdigit():
            num_node = self.new_node("num")
            val_node = self.new_node(tok)
            self.eat(tok)
            self.G.add_edge(node_F, num_node)
            self.G.add_edge(num_node, val_node)
            return node_F

        if tok == '(':
            self.eat('(')
            E_node = self.parse_E()
            if E_node is None:
                return None
            if not self.eat(')'):
                return None
            pari_node = self.new_node("pari")
            val_pari = self.new_node('(')
            pard_node = self.new_node("pard")
            val_pard = self.new_node(')')
            self.G.add_edge(node_F, pari_node)
            self.G.add_edge(pari_node, val_pari)
            self.G.add_edge(node_F, E_node)
            self.G.add_edge(node_F, pard_node)
            self.G.add_edge(pard_node, val_pard)
            return node_F

        return None

def tokenize(expr):
    tokens = []
    i = 0
    while i < len(expr):
        if expr[i].isspace():
            i += 1
        elif expr[i] in '+-*/()':
            tokens.append(expr[i])
            i += 1
        else:
            j = i
            while j < len(expr) and expr[j].isdigit():
                j += 1
            tokens.append(expr[i:j])
            i = j
    return tokens

def draw_tree(G):
    pos = nx.nx_pydot.graphviz_layout(G, prog='dot')
    labels = nx.get_node_attributes(G, 'label')
    nx.draw(G, pos, labels=labels, with_labels=True, node_size=2000,
            node_color='lightyellow', font_size=10, font_weight='bold')
    plt.show()

def main():
    expr = input("Ingresa la expresión (ejemplo: 2 + 3 * 5): ")
    tokens = tokenize(expr)
    print("Tokens:", tokens)

    parser = Parser(tokens)
    root, G = parser.parse()

    if root is None:
        print("Cadena no aceptada por la gramática.")
    else:
        print("Cadena aceptada, generando árbol...")
        draw_tree(G)

if __name__ == "__main__":
    main()
