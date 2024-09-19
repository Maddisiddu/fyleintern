 Implement Missing APIs
GET /principal/assignments:
@app.route('/principal/assignments', methods=['GET'])
def get_principal_assignments():
    principal_id = request.headers.get('X-Principal')
    if not principal_id or 'principal_id' not in principal_id:
        return jsonify({"error": "Unauthorized"}), 403
    
    # Fetch all assignments that are either in 'SUBMITTED' or 'GRADED' state
    assignments = Assignment.query.filter(Assignment.state.in_(['SUBMITTED', 'GRADED'])).all()
    return jsonify({"data": [assignment.to_dict() for assignment in assignments]})
GET /principal/teachers:
@app.route('/principal/teachers', methods=['GET'])
def get_principal_teachers():
    principal_id = request.headers.get('X-Principal')
    if not principal_id or 'principal_id' not in principal_id:
        return jsonify({"error": "Unauthorized"}), 403
    teachers = Teacher.query.all()
    return jsonify({"data": [teacher.to_dict() for teacher in teachers]})
POST /principal/assignments/grade:
Copy code
@app.route('/principal/assignments/grade', methods=['POST'])
def grade_assignment_as_principal():
    principal_id = request.headers.get('X-Principal')
    if not principal_id or 'principal_id' not in principal_id:
        return jsonify({"error": "Unauthorized"}), 403
    
    data = request.json
    assignment_id = data.get('id')
    new_grade = data.get('grade')
    
    # Fetch assignment and check if it's submitted or graded
    assignment = Assignment.query.filter_by(id=assignment_id).first()
    if not assignment or assignment.state not in ['SUBMITTED', 'GRADED']:
        return jsonify({"error": "Assignment not found or invalid state"}), 404
    
    assignment.grade = new_grade
    assignment.state = 'GRADED'
    db.session.commit()
    
    return jsonify({"data": assignment.to_dict()})
