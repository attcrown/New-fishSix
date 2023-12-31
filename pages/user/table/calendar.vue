<template>
    <v-row class="fill-height">
        <v-col>
            <v-sheet height="64">
                <v-toolbar flat color="white">
                    <v-btn outlined color="indigo" class="font-weight-bold" dark @click.stop="dialog = true;">
                        เพิ่มเวลาเรียน
                    </v-btn>
                    <v-btn outlined class="ml-4 font-weight-bold" @click="setToday">
                        วันนี้
                    </v-btn>
                    <v-btn fab text small @click="prev">
                        <v-icon small>mdi-chevron-left</v-icon>
                    </v-btn>
                    <v-btn fab text small @click="next">
                        <v-icon small>mdi-chevron-right</v-icon>
                    </v-btn>
                    <v-toolbar-title>{{ title }}</v-toolbar-title>
                    <div class="flex-grow-1"></div>
                    <v-menu bottom right>
                        <template v-slot:activator="{ on }">
                            <v-btn outlined v-on="on">
                                <span>{{ typeToLabel[type] }}</span>
                                <v-icon right>mdi-menu-down</v-icon>
                            </v-btn>
                        </template>
                        <v-list>
                            <v-list-item @click="type = 'day'">
                                <v-list-item-title>วัน</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = 'week'">
                                <v-list-item-title>สัปดาห์</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = 'month'">
                                <v-list-item-title>เดือน</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = '4day'">
                                <v-list-item-title>4 วัน</v-list-item-title>
                            </v-list-item>
                        </v-list>
                    </v-menu>
                </v-toolbar>
            </v-sheet>

            <v-dialog v-model="dialog" max-width="500">
                <v-card>
                    <v-card-title class="font-weight-bold header justify-center">ระบุรายละเอียด</v-card-title>
                    <v-container>
                        <v-form @submit.prevent="addEvent">
                            <v-text-field v-model="subject" type="text"
                                label="ระบุวิชาที่ต้องการเรียน (จำเป็น)"></v-text-field>
                            <v-text-field v-model="teacher" type="text" label="ระบุคุณครู"></v-text-field>
                            <v-text-field v-model="dayStudy" type="date" label="วันที่เรียน (จำเป็น)"></v-text-field>
                            <v-text-field v-model="startTime" type="time" label="เวลาเริ่ม (จำเป็น)"></v-text-field>
                            <v-text-field v-model="endTime" type="time" label="เวลาสิ้นสุด (จำเป็น)"></v-text-field>

                            <v-btn type="submit" color="primary" class="mr-4" @click.stop="dialog = false">
                                create event
                            </v-btn>
                        </v-form>
                    </v-container>
                </v-card>
            </v-dialog>

            <v-dialog v-model="dialogDate" max-width="500">
                <v-card>
                    <v-card-title class="font-weight-bold header justify-center">ระบุรายละเอียด</v-card-title>

                    <v-container>
                        <v-form @submit.prevent="addEvent">
                            <v-text-field v-model="name" type="text"
                                label="ระบุวิชาที่ต้องการเรียน (จำเป็น)"></v-text-field>
                            <v-text-field v-model="details" type="text" label="ระบุคุณครู"></v-text-field>
                            <v-text-field v-model="dayStudy" type="date" label="วันที่เรียน (จำเป็น)"></v-text-field>
                            <v-text-field v-model="startTime" type="time" label="เวลาเริ่ม (จำเป็น)"></v-text-field>
                            <v-text-field v-model="endTime" type="time" label="เวลาสิ้นสุด (จำเป็น)"></v-text-field>

                            <v-btn type="submit" color="primary" class="mr-4" @click.stop="dialog = false">
                                create event
                            </v-btn>
                        </v-form>
                    </v-container>
                </v-card>
            </v-dialog>

            <v-sheet height="600">
                <v-calendar ref="calendar" v-model="focus" color="primary" :events="events" :event-color="getEventColor"
                    :event-margin-bottom="3" :now="today" :type="type" @click:event="showEvent" @click:more="viewDay"
                    @click:date="setDialogDate" @change="updateRange"></v-calendar>
                <v-menu v-model="selectedOpen" :close-on-content-click="false" :activator="selectedElement"
                    offset-x>
                    <v-card color="grey lighten-4" :width="350" flat>
                        <v-toolbar :color="selectedEvent.color" dark>
                            <v-btn @click="deleteEvent(selectedEvent.id)" icon>
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                            <v-toolbar-title v-html="selectedEvent.name"></v-toolbar-title>
                            <div class="flex-grow-1"></div>
                        </v-toolbar>

                        <v-card-text>
                            <form v-if="currentlyEditing !== selectedEvent.id">
                                {{ selectedEvent.details }}
                            </form>
                            <form v-else>
                                <textarea-autosize v-model="selectedEvent.details" type="text" style="width: 100%"
                                    :min-height="100" placeholder="add note">
                                </textarea-autosize>
                            </form>
                        </v-card-text>

                        <v-card-actions>
                            <v-btn text color="secondary" @click="selectedOpen = false">
                                close
                            </v-btn>
                            <v-btn v-if="currentlyEditing !== selectedEvent.id" text
                                @click.prevent="editEvent(selectedEvent)">
                                edit
                            </v-btn>
                            <v-btn text v-else type="submit" @click.prevent="updateEvent(selectedEvent)">
                                Save
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-menu>
            </v-sheet>
        </v-col>
    </v-row>
</template>
  
<script>

export default {
    data: () => ({
        today: new Date().toISOString().substr(0, 10),
        focus: new Date().toISOString().substr(0, 10),
        type: 'month',
        typeToLabel: {
            month: 'เดือน',
            week: 'สัปดาห์',
            day: 'วัน',
            '4day': '4 วัน',
        },

        subject: null,
        teacher: null,
        dayStudy: null,
        startTime: null,
        endTime: null,
        value: null,
        color: '#1976D2', // default event color
        currentlyEditing: null,
        selectedEvent: {},
        selectedElement: null,
        selectedOpen: false,
        events: [],
        dialog: false,
        dialogDate: false
    }),
    mounted() {
        this.getEvents()
    },
    computed: {
        title() {
            const { start, end } = this
            if (!start || !end) {
                return ''
            }
            const startMonth = this.monthFormatter(start)
            const endMonth = this.monthFormatter(end)
            const suffixMonth = startMonth === endMonth ? '' : endMonth
            const startYear = start.year
            const endYear = end.year
            const suffixYear = startYear === endYear ? '' : endYear
            const startDay = start.day + this.nth(start.day)
            const endDay = end.day + this.nth(end.day)
            switch (this.type) {
                case 'month':
                    return `${startMonth} ${startYear}`
                case 'week':
                case '4day':
                    return `${startMonth} ${startDay} ${startYear} - ${suffixMonth} ${endDay} ${suffixYear}`
                case 'day':
                    return `${startMonth} ${startDay} ${startYear}`
            }
            return ''
        },
        monthFormatter() {
            return this.$refs.calendar.getFormatter({
                timeZone: 'UTC', month: 'long',
            })
        }
    },
    methods: {
        async getEvents() {
        
            const events = []
            const db = this.$fireModule.database();
            db.ref(`date_student/`).on("value", (snapshot) => {
                let appData = doc.data()
                appData.id = doc.id
                events.push(appData)
            })
            this.events = events
        },
        setDialogDate({ date }) {
            this.dialogDate = true;
            this.focus = date;
            this.dayStudy = date;
        },
        viewDay({ date }) {
            this.focus = date
            this.type = 'day'
        },
        getEventColor(event) {
            return event.color
        },
        setToday() {
            this.focus = this.today
        },
        prev() {
            this.$refs.calendar.prev()
        },
        next() {
            this.$refs.calendar.next()
        },
        async addEvent() {
            if (this.subject && this.dayStudy && this.startTime && this.endTime) {
                const db = this.$fireModule.database();
                
                await db.ref(`date_student/${this.value}/${this.dayStudy}/${this.endTime}`).update({
                    subject: this.subject,
                    teacher: this.teacher,
                    startTime: this.startTime,
                    endTime: this.endTime,
                });
                this.getEvents()
                this.subject = '';
                this.teacher = '';
                this.dayStudy = '';
                this.startTime = '';
                this.endTime = '';

            } else {
                alert('You must enter event name, start, and end time')
            }
        },
        editEvent(ev) {
            this.currentlyEditing = ev.id
        },
        async updateEvent(ev) {
            await db.collection('calEvent').doc(this.currentlyEditing).update({
                details: ev.details
            })
            this.selectedOpen = false,
                this.currentlyEditing = null
        },
        async deleteEvent(ev) {
            await db.collection("calEvent").doc(ev).delete()
            this.selectedOpen = false,
                this.getEvents()
        },
        showEvent({ nativeEvent, event }) {
            const open = () => {
                this.selectedEvent = event
                this.selectedElement = nativeEvent.target
                setTimeout(() => this.selectedOpen = true, 10)
            }
            if (this.selectedOpen) {
                this.selectedOpen = false
                setTimeout(open, 10)
            } else {
                open()
            }
            nativeEvent.stopPropagation()
        },
        updateRange({ start, end }) {
            this.start = start
            this.end = end
        },
        nth(d) {
            return d > 3 && d < 21
                ? 'th'
                : ['th', 'st', 'nd', 'rd', 'th', 'th', 'th', 'th', 'th', 'th'][d % 10]
        }
    }
}
</script>