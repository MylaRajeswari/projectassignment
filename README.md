# projectassignment
SPREADSHEET CELL MANAGEMENT APIs


from flask import Flask, jsonify, request
from flask_sqlalchemy import SQLAlchemy
from collections import defaultdict

class Spreadsheet(db.Model):
    id = db.Column(db.Integer, primary_key=True)

class Cell(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    cell_id = db.Column(db.String)
    value = db.Column(db.String)
    formula_string = db.Column(db.String)
    spreadsheet_id = db.Column(db.Integer, db.ForeignKey('spreadsheet.id'))

class CellDependency(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    cell_id = db.Column(db.String)        
    depends_on = db.Column(db.String)      
    spreadsheet_id = db.Column(db.Integer, db.ForeignKey('spreadsheet.id'))

    
 TASK 1 : Get cell Dependents   

@app.route('/spreadsheets/<int:spreadsheet_id>/cells/<cell_id>/dependents', methods=['GET'])
def get_dependents(spreadsheet_id, cell_id):
    deps = CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id, depends_on=cell_id).all()
    return jsonify([d.cell_id for d in deps])

TASK 2: Get cell precedents

@app.route('/spreadsheets/<int:spreadsheet_id>/cells/<cell_id>/precedents', methods=['GET'])
def get_precedents(spreadsheet_id, cell_id):
    deps = CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id, cell_id=cell_id).all()
    return jsonify([d.depends_on for d in deps])

TASK 3: Get Get Recalculation Order

@app.route('/spreadsheets/<int:spreadsheet_id>/recalculate-order', methods=['GET'])
def get_recalc_order(spreadsheet_id):
    changed_cell = request.args.get('changed_cell_id')
    graph = defaultdict(list)
    deps = CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id).all()
    for d in deps:
        graph[d.depends_on].append(d.cell_id)
    visited = set()
    visiting = set()
    result = []
    cycle_nodes = []
def dfs(node):
        if node in visiting:
            cycle_nodes.append(node)
            return False
        if node in visited:
            return True

  visiting.add(node)
        for neighbor in graph.get(node, []):
            if not dfs(neighbor):
                return False
        visiting.remove(node)
        visited.add(node)
        result.append(node)
        return True

  if not dfs(changed_cell):
        return jsonify({"error": "cycle_detected_involving_cells", "cells": cycle_nodes}), 400

   return jsonify({"order": result[::-1]})



if _name_ == '_main_':
    db.create_all()  
    app.run(debug=True)
