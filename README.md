# projectassignment


 assignment code


def get_dependents(spreadsheet_id, methods=['GET'])
def get_dependents(spreadsheet_id,cell_id):
    deps= CellDependency.query.filter_by(spreadsheet_id=spread,depends_on=cell_id).all()
    return jsonify([d.cell_id for d in deps])


def_precedents(spreadsheet_id, cell_id):
    deps=CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id,cell_id=cell_id).all()
    precedents=[d.depends_on for d in deps]
    return jsonify(precedents)

def get_recal_order(spreadsheet_id):
    changed_cell= request.args.get('changed_cell_id')
    from collections import defaultdict
    fraph = defaultdict(list)
    deps=CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id).all()
    for d in deps:
        graph =defaultdict(list)
        deps=CellDependency.query.filter_by(spreadsheet_id=spreadsheet_id).all()
        for d in deps:
            graph[d.depends_on].append(d.cell_id)
