<template>
    <div>
    
        <div class="col-md-6" style = "margin: 0 auto ">
    
                <div class="card" >
                        <div class="card-header" style="width: 25 rem;">
                            <label>Upload File Mark</label>
                            <form   enctype='multipart/form-data' id="quickForm" @submit.prevent="Search()">
                                <div class="form-group">
                                <input type="text" placeholder="Enter Subject ID"  v-model="information.subject_id"  class="form-control" name="subject_id">
                                <input type="text" placeholder="Year"  v-model="information.year"  class="form-control" name="year">
                                <input type="text" placeholder="Enter Semester"  v-model="information.semester"  class="form-control" name="semester">
                                <button type="submit" class="btn btn-primary" >Search</button>
                                </div>
                            </form>
                        </div>
                </div>
        </div>
    
                <div class="col-md-10" style = "margin-left:17% ">
                    <div class="card"  >
                    <div class="card-body" style="width: 20 rem;">
                        <table class="table table-bordered" >
                                <thead>
                                    <th width="5%">STT</th>
                                    <th width="5%">sinhvien_id</th>    
                                    <th>sinhvien Name</th>                              
                                    <th>Subject_id</th>
                                    <th>Subject_name</th>
                                    <th>Teacher_id</th>
                                    <th>Teacher Name</th>
                                    <th>Title</th>
                                    <th>Note</th>
                                    <th>Mark</th>
                                    <th >Download</th>
                                </thead>
                                <tbody>
    
                                    <tr v-for="reports in listReports" :key="reports.id">
                                        <th scope="row">{{ reports.report_id }}</th>
                                        <td>{{ reports.sinhvien_id }}</td>
                                        <td>{{ reports.first_name }}</td>
                                        <td>{{ reports.subject_id }}</td>
                                        <td>{{ reports.name }}</td>
                                        <td>{{ reports.teacher_id }}</td>
                                        <td>{{ reports.teacher_first_name }}</td>
                                        <td>{{ reports.title }}</td>
                                        <td>{{ reports.report_note }}</td>
                                        <td>{{ reports.mark }}</td>
                                        <td>
                                          <button class="btn-download" 
                                                @click.prevent="downloadFile(reports.report_id, )" >
                                                <i class="fa fa-download">Download</i>
                                            </button>
                                        </td>
                                    </tr>
                                </tbody>
                        </table>
                        <div style = "margin-left: 90% ; margin-top:10px " >
                            <button class="btn btn-success"
                                                @click.prevent="ExportMark(listReports)">
                                                Export Mark
                            </button>
                        </div>
                    </div>
    
                    </div>
                </div>
            
    
    
    </div>
    
    </template>
    
    <script>
    import axios from 'axios'
    
    
    export default {
        props: {
                base_url: String
            },
            data() {
                return {
                    information: {
                    subject_id: "",
                    semester: "",
                    year: "",
                    
                    },
                    listReports: [],
                    listReportsFilter: [],
                    error: null,
                    setmark:{
                        report_id:"",
                        mark:"",
                        },
                    output:"",
                    success:"",
                    message:"",
                    download:{report_id:"",},
                    export:{report_id:"",},
                }
            },
    
         methods: {
                
                async Search() {
                    console.log(this.informtion);
                    try {
                        const url = "http://127.0.0.1:8000/exportmark/search-all-report";
                        const response = await axios.post(url, this.information,{
                            'subject_id': this.subject_id,
                            'semester': this.semester,
                            'year': this.year,
                        });
                        this.listReports = response.data;
                        console.log(response.data);
                    }
                     catch (error) {
                        console.log(error);
                    }
                },
                    async setMark() {
                    console.log(this.setmark);
                    try {
                        const url = "http://127.0.0.1:8000/setmark/set-mark-report";
                        const response = await axios.post(url, this.setmark,{
                            report_id:"",
                            mark:"",
                        });
                        console.log(response.data);
                    }
                     catch (error) {
                        console.log(error);
                    }
    
                },
                async downloadFile(report_id)
                 {
    
                    axios.get('http://127.0.0.1:8000/setmark/download-file-report',{params:{report_id:report_id}})
                    .then(
                        (response) => {window.open( "http://127.0.0.1:8000/setmark/download-file-report?report_id="+report_id, "_blank");
                      });
                                     
                    
                 },
                
             ExportMark(listReports){
                const object = this.listReports;
                    const header_row=
                        {
                      'report_id': '',
                      'sinhvien_id': '',
                      'subject_id': '',
                      'name': '',
                      'first_name': '',
                      'teacher_id': '',
                      'teacher_first_name ': '',
                      'title': '',
                      'report_note': '',
                      'mark': '',
                      
                        }
                    const header = Object.keys(header_row);
                    const replacer = function(key,value) {return value === null ? '' : value}
                    const csv = [
                        header.join(',')  ,  //header row first
                        ...object.map(row => header.map(fieldName => JSON.stringify(row[fieldName], replacer) ).join(','))                     
                    ].join('\r\n')
                    var fileURL = window.URL.createObjectURL(new Blob([csv]));
                    var fileLink = document.createElement('a');
                    fileLink.href = fileURL;
                    fileLink.setAttribute('download', 'test.csv');
                    document.body.appendChild(fileLink);  
                    fileLink.click();
            }
        },
    
    
    }
    </script>
    
    <style>
    
    </style>