Rails.application.routes.draw do root 'sessions#new'
get 'register', to: 'patients#new' post 'register', to: 'patients#create' get 'login', to: 'sessions#new'
post 'login', to: 'sessions#create' delete 'logout', to: 'sessions#destroy' namespace :receptionist do
get '/', to: 'patients#index'
resources :patients end
namespace :doctor do get '/', to: 'patients#index'
get 'graph', to: 'patients#graph' resources :patients, only: [:index]
`app/controllers/receptionist/patients_controller.rb`class Receptionist::PatientsController < ApplicationController before_action :require_login
def index
@patients = Patient.all end
def new
@patient = Patient.new
end
def create
@patient = Patient.new(patient_params) if @patient.save
redirect_to receptionist_patients_path, success: 'Patient registered successfully' else
render :new def edit
@patient = Patient.find(params[:id]) def update
@patient = Patient.find(params[:id])

if @patient.update(patient_params)
redirect_to receptionist_patients_path, success: 'Patient details updated successfully'
else
render :edit
end end
def destroy
@patient = Patient.find(params[:id])
@patient.destroy
redirect_to receptionist_patients_path, success: 'Patient deleted successfully'
end
private
def patient_params
params.require(:patient).permit(:name, :email, :phone_number)
end
def require_login unless current_user
redirect_to login_path, error: 'You must be logged in to access this page' end
end end
`app/controllers/doctor/patients_controller.rb`:
class Doctor::PatientsController < ApplicationController
before_action :require_login
def index
@patients = Patient.all

end
def graph
# Code for generating graph
# You can use any charting library like Chart.js or Google Charts
end
private
def require_login unless current_user
redirect_to login_path, error: 'You must be logged in to access this page' end
end end
`app/views/sessions/new.html.erb`: <h1>Login</h1>
<%= form_tag login_path do %>
<%= label_tag :email %>
<%= email_field_tag :email %>
<%= label_tag :password %>
<%= password_field_tag :password %> <%= submit_tag 'Login' %>
<% end %> `app/views/receptionist/patients/index.html.erb`: <h1>Receptionist Portal</h1>
<%= link_to 'New Patient', register_path %>
<table> <thead>
<tr>

<th>Name</th> <th>Email</th> <th>Phone Number</th> <th>Action</th>
</tr> </thead> <tbody>
<% @patients.each do |patient| %> <tr>
<td><%= patient.name %></td> <td><%= patient.email %></td> <td><%= patient.phone_number %></td> <td>
<%= link_to 'Edit', edit_receptionist_patient_path(patient) %>
<%= link_to 'Delete', receptionist_patient_path(patient), method: :delete, data: { confirm: 'Are you sure?' } %>
</td> </tr>
<% end %> </tbody>
</table> `app/views/receptionist/patients/new.html.erb`: <h1>New Patient</h1>
<%= form_for @patient, url: register_path do |f| %>
<%= render 'shared/errors', object: @patient %> <%= f.label :name %>
<%= f.text_field :name %>
<%= f.label :email %>
<%= f.email_field :email %>
<%= f.label :phone_number %> <%= f.text_field :phone_number %> <%= f.submit 'Register' %>
<% end %>
 
rails generate model Patient name:string email:string phone_number:string rails db:migrate
Database code :-
import psycopg2
def connect_to_database():
conn = psycopg2.connect( host="localhost", database="mydatabase", user="myuser", password="mypassword"
)
return conn
def create_table():
conn = connect_to_database() cursor = conn.cursor() cursor.execute(
CREATE TABLE IF NOT EXISTS users ( id SERIAL PRIMARY KEY,
name VARCHAR(100),
age INTEGER
) )
conn.commit() cursor.close() conn.close()
def insert_data(name, age): conn = connect_to_database() cursor = conn.cursor() cursor.execute(
INSERT INTO users (name, age) VALUES (%s, %s) (name, age)

) conn.commit() cursor.close() conn.close()
def retrieve_data():
conn = connect_to_database() cursor = conn.cursor() cursor.execute(
SELECT * FROM users )
rows = cursor.fetchall() cursor.close() conn.close()
return rows
In this code, the `psycopg2` library to connect to a PostgreSQL database. The `connect_to_database` function establishes a connection to the database using the connection parameters (host, database, user, and password).
