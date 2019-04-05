---
layout: page
title: Baby ML
permalink: /guides/baby-ml/
---

Sometimes you, like me, forget how to actually use basic machine learning, so here is a scikit-learn starter kit for text in python.

```
from nltk import bigrams
from sklearn.linear_model import LogisticRegression
import json


def vectorize(header, line):
    unfiltered = ['{}{}'.format(i[0], i[1]) for i in list(bigrams(line.strip()))]
    return [unfiltered.count(h) for h in header]


def train(true_file, false_file):
    header_set = []

    true_list = []
    with open(true_file, 'r') as fp:
        for line in fp:
            true_list.append(['{}{}'.format(i[0], i[1]) for i in list(bigrams(line.strip()))])
            header_set.extend([i for i in true_list[-1]])
            header_set = list(set(header_set))

    false_list = []
    with open(false_file, 'r') as fp:
        for line in fp:
            false_list.append(['{}{}'.format(i[0], i[1]) for i in list(bigrams(line.strip()))])
            header_set.extend([i for i in false_list[-1]])
            header_set = list(set(header_set))

    header = list(header_set)

    true_vector = []
    for e in true_list:
        true_vector.append([e.count(h) for h in header])

    false_vector = []
    for e in false_list:
        false_vector.append([e.count(h) for h in header])

    X = true_vector + false_vector
    y = [1] * len(true_vector) + [0] * len(false_vector)
    return header, X, y


def load(vector_file):
    with open(vector_file, 'r') as fp:
        return json.load(fp)


def save(vector_file, header, X, y):
    with open(vector_file, 'w') as fp:
        json.dump({'h': header, 'X': X, 'y': y}, fp)


def run():
    #header, X, y = train('emails.txt', 'not-emails.txt')
    #save('vector.json', header, X, y)

    model = load('vector.json')
    h = model['h']
    X = model['X']
    y = model['y']
    clf = LogisticRegression(random_state=0, solver='lbfgs', multi_class='multinomial').fit(X, y)
    print(clf.predict([vectorize(h, 'noreply@gmail.com')]))


if __name__ == '__main__':
    run()

```