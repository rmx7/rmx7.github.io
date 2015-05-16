<table id="skill-statistic">
    {% for skill in page.skills %}
    <tr>
        <td>{{ skill[0] }}</td>
        <td>
            {% for i in (1..5) %}
                {% if i <= skill[1] %}
                    &starf;
                {% else %}
                    &star;
                {% endif %}
            {% endfor %}
        </td>
    </tr>
    {% endfor %}
</table>
